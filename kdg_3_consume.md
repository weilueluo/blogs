# Consume

> [!NOTE]
>
> not done



A reason for using Kafka is the rate of producer exceed the rate of consumer, you want some buffer in the middle to avoid overflowing the consumer.

| 1 Consumer Group 4 Partitions                                | 2 Consumer Group 4 Partitions                                |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20240923164325453](https://raw.githubusercontent.com/weilueluo/note-images/master/2024/09/upgit_20240923_1727106205.png) | ![image-20240923164411915](https://raw.githubusercontent.com/weilueluo/note-images/master/2024/09/upgit_20240923_1727106251.png) |
| **4 Consumer Group 4 Partitions**                            | **5 Consumer Group 4 Partitions**                            |
| ![image-20240923164424201](https://raw.githubusercontent.com/weilueluo/note-images/master/2024/09/upgit_20240923_1727106264.png) | ![image-20240923164446993](https://raw.githubusercontent.com/weilueluo/note-images/master/2024/09/upgit_20240923_1727106287.png) |
| **Multiple Consumer Groups**                                 |                                                              |
| ![image-20240923164849123](https://raw.githubusercontent.com/weilueluo/note-images/master/2024/09/upgit_20240923_1727106529.png) |                                                              |

- We cant implement *fan-out* pattern using one consumer group, we need to create multiple of them
- No point adding more consumers than producers, some consumers will be idle.
- 