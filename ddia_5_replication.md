# Replication

> [!NOTE]
>
> not done

You probably already know having a back up is good practice, when your main database fails, you can restore data back in time instead of losing everything. Replicates are also copies of your data in multiple machines, but they carries more responsibilities, by strategically placing these backup in different geographic locations, you can reduce latency of your services by allowing these copies to serve customer requests nearby.

In order to serve customer requests, we need to ensure our replica are synced with the latest data from the original copy in real-time. 