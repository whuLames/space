# 代码结构



## Push mode

- compute_shared_degrees_kernel
- expand_shared_node_kernel
- Expand_shared_node_query_parallel_kernel
- Expand_shared_node_warp_kernel
- expand_shared_node_degree: low medium high
- 

## Pull mode





# 代码结构修改思路

- pull模式下的scan 和 compact 我个人感觉是需要保留的

  - 做一个进一步的封装，pull kernel的调用分为 计算 + post processing
  - 关于hybird场景下 pull `compute_candidate_pull` 其实是根据算法的不同来决定relax的策略，这里可能的修改思路是这样
    - 把算法包装为algorithm，然后不同的算法提供不同的relax策略，我们可以在 ``compute_candidate_pull` 中直接调用algorithm.relax 来获取candidate value
    - 或者依旧按照现在的方式做switch 枚举， 但要添加算法注册的借口

- 整体来看 hybird下的pull其实还行，逻辑还是比较清晰的, push下可能有几点需要改

  - 关于计算出candidate之后，candidate value 和 original value的对比应该怎么做？

    - 目前非hybird kernel里面的做法是直接写小于号![image-20260709223738234](/Users/zyl/Library/Application Support/typora-user-images/image-20260709223738234.png)

    - 这样的写法不如

      ![image-20260709223810569](/Users/zyl/Library/Application Support/typora-user-images/image-20260709223810569.png)

      这里可能需要修改一下

- value layout

  - 目前的layout是V*Q，后续我们可能要改成Q * V 来适配dynamic batch的策略
    - 当前push下的基本原则可以理解为一个thread负责一个vertex的所有query，那么一个thread在读取数据时天然会读取一个vertex的连续多个values，理论上这是个连续读取，但是现在的实现是先判断某个query是否被激活，也就是不是每次都读32个
    - **先不谈论如果我们加入连续读取的优化（即每个thread直接读连续的数据，即使这个数据我们用不上）**
      - **或许这个加入连续读取优化是必要的？**
    - 这样来看，其实我们换成Q * V，我感觉应该性能差异不大，因为全是离散的global memory 读取
      - 但Q * V可能会更损伤cache
  - 对于Pull模式计算的影响
    - 看起来也会更好？
    - 因为现在pull计算的逻辑是一个thread读取该thread对应的query下，该thread对应neighbors的所有数据
      - 如果是V*Q，其实是跨行读取
      - 如果是Q*V，反而是对某一行的多个不一定连续的位置坐读取，这样甚至更有可能出发cache line的缓存，完全可以尝试一下
    - **明天要在基础的kernel上尝试一下pull kernel 在两种layout下的性能表现**
    - 我的想法完全错误，性能差距很大
      - 看起来访存合并的对kernel性能的影响是巨大的

- push模式下的 query parallel是否有明显的性能优势
  - 即一个warp负责一条边
  - 而不是一个thread负责一个节点的所有query
  - 看起来前者的balance会好一些
  - 明天要测试一下
  - 
