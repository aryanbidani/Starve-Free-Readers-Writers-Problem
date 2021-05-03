### Pseudocode of Starve-Free-Readers-Writers-Problem
```
/* This code describes the solution to third readers-writers problem*/

unsigned int rd_count = 0; // variable to maintain number of readers reading
semaphore rd_mutex; // makes sure that only one reader at a time can change the readers count
semaphore get_access; // decides either the readers or the writer will have access to critical section
semaphore order; // helps in preserving the order of arrival for reader/writer

void read(){

    wait(order); // preserve the order of arrival
    wait(rd_mutex); // get the mutex to change readers count
    
    if(rd_count==0){
    // If, it is the first reader, get the access mutex to avoid writer while current readers are reading
        wait(get_access);
    }
    
    rd_count++; // increment the readers count
    signal(order); // release the order semaphore as the work is done
    signal(rd_mutex); // release reader semaphore to allow other readers who need it
    
    read_resource(); // reading of resource
    
    wait(rd_mutex); // get the mutex to change readers count
    rd_count--;
    
    if(rd_count==0){
    // If, it is the last reader, release the get_access semaphore which could allow the writers to gain access
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
