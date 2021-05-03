# Starve-Free-Readers-Writers-Problem

Readers-writers problem is one of the most common problem in process synchronization. In this problem there are two type of users the Readers, who wants to read the shared resource and the Writers, who wants to modify the shared resourcse. There are three variations to this problem.

### First readers–writers problem 
This problem allows that the when there is atleast one reader accessing the resource, then new readers can also enter the critical section to read that resource. However, in this case the writer may lead to starvation as it may not get a chance to modify the resourcse as new readers may continiously enter to read the resource.
### Second readers–writers problem
This is also similar to the above problem, however here the readers may starve. The writer are given preferene in this problem. The reader must wait until the last writer exits the critical section by modifying the resource and release the lock to allow readers to acces the resource.  
### Third readers–writers problem
This problem overcomes the drawbacks of previous two problems of starvation and therefore is also known as the **Starve-Free-Readers-Writers-Problem**. It will give priority to the resources in the order of their arrival. For example if a writers wants to write to the resource then it will wait untill the current readers execute their tasks. Meanwhile, other readers accessing the resourcse would not be allowed to do so.

### Explanation of the Code

We will make use of three semaphores:
- **rd_mutex -->**  It would be used while updating the readers count. Therefore it would only be availabel to readers method.
- **get_access -->**  This would be either in control of readers or the writer. If the readers are reading, then if writer would try to access the critical section would get blocked and vice versa. However if one reader is reading and another reader try to access then there won't be any problem. This semaphore get updated at 3 instances. First, when 1st reader arrives. Secondly when the last reader left the critical section. Lastly, when any writer will would write to the resource.
- **order -->** This semaphore is used at the start of entry section of readers/writers code. This only checks that if there is any process already waiting for its turn. If so, it gets blocked. If not, then it access the semaphore and no new reader/writer could now execute before this process.  Therefore, it helps in preserving the order of process.

We would also make use of a variable **rd_count** to update the number of readers at a particular time. 

For the read method we would first call wait for **order** and **rd_mutex**. If any process is already in queue order would be 1 and thus calling process would be blocked. Otherwise it would make order "1". **rd_mutex** would check that no other process is updating the readers count. If reader count was 0 then don't allow writer to access the critical section. After reader count is updated both the semaphore are released. After reading of resource read_count is decremented by getting hold of rd_mutex. If reader count is 0, writers can now access critical section.

For write method we would first check the **order** semaphore and then writer would get access with the **get_access** semaphore. Since, the order would be preserved we could release the **order** semaphore. Then writers modifies the resourcse and finally release **get_access**.

In this way no process would starve and our purpose is achieved.
