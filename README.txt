Buffer Manager

--------------------------------------
Running the Program:

Step 1 - Go to Project root (assign1) using Terminal.
Step 2 - $ make clean (to delete old compiled .o files)
Step 3 - $ make (to compile all project files) 
Step 4 - $ make run_test1 (to run "test_assign1_1.c" file)

To run additional Test Cases:

Step 1 - Repeat Step 1 to 3 from previous steps.
Step 2 - $ make test2 (to compile Custom test file "test_assign1_2.c")
Step 3 - $ make run_test2 (to run "test_assign1_2.c" file)

--------------------------------------

Function Description:
--------------------------------------
1. BUFFER POOL FUNCTIONS
--------------------------------------

The buffer pool related functions are used to create a buffer pool for an existing page file on disk. The buffer pool is created in memory while the page file is present on disk. We make the use of Storage Manager (Assignment 1) to perform operations on page file on disk.

a) initBufferPool()
	- This function creates a new buffer pool with numPages equal to the buffer size and the page replacement scheme strategy. pageFileName stores the name of the page file whose pages are stored in memory. Setting the initialization data to 0 or NULL and initializing all of the pages' values. All other counters are set to zero. Parameters for the page replacement technique can be sent via stratData.

b) shutdownBufferPool()

	- This function is used to destroy a buffer pool. It also frees up all resources related with the buffer pool, as well as the memory reserved for page frames. We used forceFlushPool() to see if the buffer pool included any dirty pages; if so, those pages should be written back to disk before the pool is destroyed. If the page is being used by anyone, it will produce an error indicating that the page is being utilized. If not, it will free up all of the pages and set the size to NULL.

c) forceFlushPool()

	- This function writes all the modified pages to the disk. this function checks all pages from the bufferpool and checks that the page is modified by any other program if not it will return custom message ------------ . it also checks that the page is accessed by someoen if not it will return an custom message -----------. If page is modified and is not been written by anyone than it will write all teh modified data to the disc and then increment the write counter.

--------------------------------------
2. PAGE MANAGEMENT FUNCTIONS
--------------------------------------

The page management related functions are used to load pages from disk into the buffer pool (pin pages), remove a page frame from buffer pool (unpin page), mark page as dirty and force a page fram to be written to the disk.

a) pinPage()
--> This method takes the page from the page file on disk and saves it in the buffer pool, pinning the page number pageNum. It checks if the buffer pool has any empty space before pinning a page. If the page frame contains an empty space, it can be placed in the buffer pool; otherwise, a page replacement method must be employed to replace a page in the buffer pool. We've introduced page replacement techniques such as FIFO, LRU, LFU, and CLOCK, which are utilized when pinning a page. Which pages need to be replaced are determined by page replacement algorithms. If the page in question is dirty, it is checked. If dirtyBit = 1, the contents of the page frame are written to the page file on disk, and the new page is inserted in the same position as the previous one.

b) unpinPage()
--> This method removes the pin from the given page. The pageNum of the page determines which page will be unpinned. After utilizing a loop to locate the page, it decrements the fixCount of that page by 1, indicating that the client is no longer accessing it.

c) makeDirty()
--> The dirtyBit of the provided page frame is set to 1 by this function. It repeatedly checks each page in the buffer pool for the page frame using pageNum, and when the page is found, dirtyBit = 1 is set for that page.

d) forcePage()
--> This page saves the contents of the given page frame to a disk-based page file. It uses a loop construct to locate the requested page using pageNum by examining all the pages in the buffer loop. When the page is located, the content of the page frame is written to the page file on disk using the Storage Manager functions. It sets dirtyBit = 0 for that page after writing.

--------------------------------------
3. STATISTICS FUNCTIONS
--------------------------------------

a) getFrameContents()

	- This function returns an array of PageNumbers with the same size as the buffer. It will first check to see if the buffer pool exists, and if it does, it will display an error message. If everything is fine, it will loop through all of the page frames in the buffer pool to acquire the pageNum value of the page frames in the buffer pool.

b) getDirtyFlags()

	- This function returns an array of bools with the same size as the buffer. It will first check to see if the buffer pool exists, and if it does, it will return an error. If all goes well, it will loop through all of the page frames in the buffer pool to extract the dirty bit value of the page frames in the buffer pool. If the ith element is TRUE, the page in the ith page frame is soiled. Empty page frames are regarded as "clean."

c) getFixCounts() 

	- This function returns an array of ints with the same size as the buffer. It will first check to see if the buffer pool exists, and if it does, it will return an error. If all goes well, it will loop through all of the page frames in the buffer pool to extract the value of the page frames in the buffer pool. where the ith element is the page fix count recorded in the ith page frame In the case of empty page frames, it will return 0.

d) getNumReadIO()

	- This function returns the number of pages read from disk after the buffer pool has been initialized. It will first check to see if the buffer pool exists, and if it does not, it will return an error. If all goes well, it will return the rear index variable + 1 because the index starts with 0.

e) getNumWriteIO()

	- This function returns the number of pages written to the page file since the buffer pool has been initialized. It will first check to see if the buffer pool exists, and if it does not, it will return an error. If all goes well, it will return the write counter value.

4. PAGE REPLACEMENT ALGORITHM FUNCTIONS
=========================================

The page replacement strategy functions implement FIFO, LRU, LFU, CLOCK algorithms which are used while pinning a page. If the buffer pool is full and a new page has to be pinned, then a page should be replaced from the buffer pool. These page replacement strategies determine which page has to be replaced from the buffer pool.

FIFO(...)
First In First Out (FIFO) is the most basic page replacement strategy used where the pages are given priority based on the order in which they arrive in the buffer pool. Hence, a page which arrived earlier will be deleted first. Once the page has been found, the content of the page frame is written to the page file on disk, and the new page is added to that place.

LFU(...)
Least Frequent Used (LFU ) is a page replacement strategy used where the pages are given priority based on the order in which they are used number of times(i.e their frequency). The variable (field) refNum keeps a count of of the page frames being accessed by the client. So, while utilizing LFU, all we have to do is determine the position of the page frame with the lowest refNum value. The content of the page frame is then written to the page file on disk, and the new page is added at that place. We also save the position of the least frequently used page frame in a variable called "lfuPointer" so that we can replace a page in the buffer pool later. From the second page replacement forward, the number of iterations is reduced.

LRU(...)
Least Recent Used (LFU ) is a page replacement strategy used where the pages are given priority based on the order in which they are used (i.e their time until last use). Least Recently Used (LRU) removes the page frame which hasn't been used for a long time (least recent) amongst the other page frames in the buffer pool. The variable (field) hitNum in each page frame serves this purpose. hitNum keeps a count of of the page frames being accessed and pinned by the client. Also a global variable "hit" is used for this purpose. So when we are using LRU, we just need to find the position of the page frame having the lowest value of hitNum.We then write the content of the page frame to the page file on disk and then add the new page at that location.

CLOCK(...)
The CLOCK algorithm maintains track of the buffer pool's most recently inserted page frame. Also, to point the page frames in the buffer pool, we utilize a clockPointer, which is a counter. When a page has to be changed, we look at the location of the "clockPointer." Replace that page with the new page if the hitNum of that position's page is not 1 (i.e. it wasn't the final page uploaded).If hitNum = 1, we change it to hitNum = 0 and increment clockPointer, which means we move to the next page frame to verify the same thing. This procedure continues until we identify a suitable replacement for the page. We set hitNum = 0 to avoid being stuck in an infinite loop.
 
