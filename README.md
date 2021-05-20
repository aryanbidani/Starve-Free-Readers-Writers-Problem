# Starve-Free-Readers-Writers-Problem

Readers-writers problem is one of the most common problem in process synchronization. In this problem there are two type of process the Readers, who wants to read the shared resource and the Writers, who wants to modify the shared resource. Therefore there needs to be a synchronization in such a way that while, writer is writing, no other process should be able to access the critical section. On the otherhand, more than one readers could be allowed to read the resource at a particular time.  There are three variations to this problem.

### First readers–writers problem 
This problem allows that the when there is atleast one reader accessing the resource, then new readers can also enter the critical section to read that resource. However, in this case the writer may lead to starvation as it may not get a chance to modify the resourcse as new readers may continiously enter to read the resource.
### Second readers–writers problem
This is also similar to the above problem, however here the readers may starve. The writer are given preferene in this problem. The reader must wait until the last writer exits the critical section by modifying the resource and release the lock to allow readers to acces the resource.  
### Third readers–writers problem
This problem overcomes the drawbacks of previous two problems of starvation and therefore is also known as the **Starve-Free-Readers-Writers-Problem**. It will give priority to the resources in the order of their arrival. For example if a writers wants to write to the resource then it will wait untill the current readers execute their tasks. Meanwhile, other readers accessing the resourcse would not be allowed to do so if there is a writer waiting for its turn before them. Below is the detailed implementation of Third readers–writers problem using semaphores.

### Implementation of Third readers–writers problem

We will make use of the variable **semaphore**, which would be a struct conatining an integer and also a queue to store the blocked processes. Two methods `wait()` and `signal()` would be used upon these semaphores for proper process synchronization.  
  
Whenever **wait()** is called then the value of semaphore is decremented and if it becomes negative then the process invoking the method would add up to blocked process queue. If it is positive then execution continues further without any problem.  
  
Whenever **signal()** is called then the value of semaphore is incremented and if it is non-positive then one process from the blocked queue is evicted to begin its execution again.  
We will make use of three semaphores:
- **rd_mutex -->**  It would be used while updating the readers count. Therefore it would only be availabel to readers method.
- **get_access -->**  This would be either in control of readers or the writer. If the readers are reading, then if writer would try to access the critical section would get blocked and vice versa. However if one reader is reading and another reader try to access then there won't be any problem. This semaphore get updated at 3 instances. First, when 1st reader arrives. Secondly when the last reader left the critical section. Lastly, when any writer will would write to the resource.
- **order -->** This semaphore is used at the start of entry section of readers/writers code. This only checks that if there is any process already waiting for its turn. If so, it gets blocked. If not, then it access the semaphore and no new reader/writer could now execute before this process.  Therefore, it helps in preserving the order of process.

We would also make use of a variable **rd_count** to update the number of readers at a particular time.
```c++

struct semaphore{
int count = 1; // holds the semaphore value
queue* q =  new queue(); // holds the blocked process in queue
};

wait(semaphore s){
s.count--; // decrement the count
  if(s.count<0){
    s.q->enqueue(pid); // Add the process to queue of blocked process
    block();
  }
}

signal(semaphore s){
s.count++; // increment the count
  if(s.count<=0){
   wakeup(s.q->dequeue()); // Remove the first process from queue and start executing it
  }
}

unsigned int rd_count = 0; // variable to maintain number of readers reading
semaphore rd_mutex; // makes sure that only one reader at a time can change the readers count
semaphore get_access; // decides either the readers or the writer will have access to critical section
semaphore order; // helps in preserving the order of arrival for reader/writer


```

#### Pseudocode of Starve-Free-Readers-Writers-Problem


```c++
/* This code describes the solution to third readers-writers problem also known as the
Starve-Free-Readers-Writers-Problem*/

void read(){

    wait(order); // preserve the order of arrival
    wait(rd_mutex); // get the mutex to change readers count
    
    if(rd_count==0){
    // If, it is the first reader, claim the access mutex to avoid writers while current readers are reading
        wait(get_access);
    }
    
    rd_count++; // increment the readers count
    signal(order); // release the order semaphore as the work is done
    signal(rd_mutex); // release reader semaphore to allow other readers who need it
    
    read_resource(); // reading of resource
    
    wait(rd_mutex); // get the mutex to change readers count
    rd_count--;
    
    if(rd_count==0){
    /* If, it is the last reader, release the get_access semaphore which could allow the 
    /potential writers to gain access to the critical section */
        signal(get_access);
    }
    
    signal(rd_mutex); // as the count is updated, so release the mutex
    
}

void write(){

    wait(order); // preserve the order of arrival
    wait(get_accesss); // get the access mutex to avoid readers while writing
    signal(order); // release order semaphore as the work is done
    
    write_resource(); // writing of resource takes place 
    
    signal(get_access); // release the access to the resource to allow other users to get entry
    
}
```
#### Explanation

For the read method we would first call wait for **order** and **rd_mutex**. If any process is already in queue, calling process would be blocked. Otherwise it would make increment order. **rd_mutex** would check that no other process is updating the readers count. If reader count was 0 then don't allow writer to access the critical section. After reader count is updated both the semaphore are released. After reading of resource read_count is decremented by getting hold of **rd_mutex**. If reader count is 0, writers can now access critical section and the **get_access** semaphore.

For write method we would first check the **order** semaphore and then writer would get access with the **get_access** semaphore. Since, the order is now preserved we could release the **order** semaphore. Then writers modifies the resourcse and finally release **get_access** semaphore.

This was the implementation of Starve-Free-Readers-Writers-Problem. In this manner no process would starve and our purpose is fulfiled.
