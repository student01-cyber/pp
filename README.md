# pp
### Semaphore  
 
#include <stdio.h> 
#include <pthread.h> 
#include <semaphore.h> 
#include <unistd.h> 
 
int buffer;        // Only 1 item space 
int count = 0;     // Buffer size (0 or 1) 
 
sem_t empty;       // Tells if buffer is empty 
sem_t full;        // Tells if buffer has data 
 
void* producer() { 
    for (int i = 0; i < 5; i++) { 
        sem_wait(&empty);  // Wait if buffer is not empty 
 
        buffer = i;        // Produce item 
        count = 1; 
        printf("Produced: %d | Buffer size: %d\n", i, count); 
 
        sem_post(&full);   // Say buffer is full now 
        sleep(1);          // Wait for 1 second 
    } 
    return NULL; 
} 
 
void* consumer() { 
    for (int i = 0; i < 5; i++) { 
        sem_wait(&full);   // Wait if buffer is empty 
 
        int item = buffer; // Consume item 
        count = 0; 
        printf("Consumed: %d | Buffer size: %d\n", item, count); 
 
        sem_post(&empty);  // Say buffer is empty now 
        sleep(1);          // Wait for 1 second 
    } 
    return NULL; 
} 
 
int main() { 
    pthread_t p, c; 
 
    sem_init(&empty, 0, 1);  // Buffer starts empty 
    sem_init(&full, 0, 0);   // Nothing to consume 
 
    pthread_create(&p, NULL, producer, NULL);  // Start producer 
    pthread_create(&c, NULL, consumer, NULL);  // Start consumer 
 
    pthread_join(p, NULL);  // Wait for    r to finish 
    pthread_join(c, NULL);  // Wait for consumer to finish 
 
    sem_destroy(&empty); 
    sem_destroy(&full); 
 
    return 0; 
} 
 
 
 
### Banker Algorithm  
#include <stdio.h> 
#include <stdbool.h> 
 
int main() { 
    int n, m; 
 
    printf("Enter number of processes: "); 
    scanf("%d", &n); 
 
    printf("Enter number of resource types: "); 
    scanf("%d", &m); 
 
    int alloc[n][m], max[n][m], need[n][m], avail[m]; 
    int finish[n], safeSeq[n]; 
 
    // Input Allocation matrix 
    printf("\nEnter Allocation matrix (%d x %d):\n", n, m); 
    for (int i = 0; i < n; i++) { 
        printf("P%d: ", i); 
        for (int j = 0; j < m; j++) { 
            scanf("%d", &alloc[i][j]); 
        } 
    } 
 
    // Input Max matrix 
    printf("\nEnter Max matrix (%d x %d):\n", n, m); 
    for (int i = 0; i < n; i++) { 
        printf("P%d: ", i); 
        for (int j = 0; j < m; j++) { 
            scanf("%d", &max[i][j]); 
        } 
    } 
 
    // Input Available resources 
    printf("\nEnter Available resources (%d values):\n", m); 
    for (int i = 0; i < m; i++) { 
        scanf("%d", &avail[i]); 
    } 
 
    // Calculate Need matrix = Max - Allocation 
    for (int i = 0; i < n; i++) { 
        finish[i] = 0; 
        for (int j = 0; j < m; j++) { 
            need[i][j] = max[i][j] - alloc[i][j]; 
        } 
    } 
 
    // Find safe sequence 
    int count = 0; 
    while (count < n) { 
        bool found = false; 
        for (int i = 0; i < n; i++) { 
            if (!finish[i]) { 
                bool canRun = true; 
                for (int j = 0; j < m; j++) { 
                    if (need[i][j] > avail[j]) { 
                        canRun = false; 
                        break; 
                    } 
                } 
                if (canRun) { 
                    for (int j = 0; j < m; j++) { 
                        avail[j] += alloc[i][j]; 
                    } 
                    safeSeq[count++] = i; 
                    finish[i] = 1; 
                    found = true; 
                } 
            } 
        } 
 
        if (!found) { 
            printf("\nSystem is not in a safe state (Deadlock may 
occur).\n"); 
            return 0; 
        } 
    } 
 
    // Safe state 
printf("\nSystem is in a safe state.\nSafe sequence: "); 
for (int i = 0; i < n; i++) { 
printf("P%d ", safeSeq[i]); 
} 
printf("\n"); 
return 0; 
} 


### Shell programming  
Program fo getid(),fork() 
echo "parent process id: $$" 
sleep 1 & 
child_pid=$! 
echo "child_pid: $child_pid" 
wait 
echo "parent process ID: $$" 
 
AND  
 
#include<stdio.h> 
#include<stdlib.h> 
#include<unistd.h> 
 
int main(){ 
    int pid; 
    printf("before the process forkid=%d",getpid()); 
    printf("\n"); 
    pid=fork(); 
    if(pid==0){ 
        printf("after fork chid process id=%d",getpid()); 
         printf("\n"); 
    } 
    else { 
        printf("after fork parent process id=%d",getpid()); 
         printf("\n"); 
    } 
    return 0; 
} 
 
 
### CPU Scheduling FCFS 
#include <stdio.h> 
 
int main() { 
    int n, i, j; 
    int at[20], bt[20], wt[20], tat[20], pid[20]; 
    int total_wt = 0, total_tat = 0; 
    int temp; 
 
    printf("Enter number of processes: "); 
    scanf("%d", &n); 
 
    // Input process IDs, Arrival Time and Burst Time 
    for(i = 0; i < n; i++) { 
        pid[i] = i + 1; 
        printf("Enter Arrival Time for Process %d: ", pid[i]); 
        scanf("%d", &at[i]); 
        printf("Enter Burst Time for Process %d: ", pid[i]); 
        scanf("%d", &bt[i]); 
    } 
 
    // Sort processes by Arrival Time (simple bubble sort) 
    for(i = 0; i < n-1; i++) { 
        for(j = i+1; j < n; j++) { 
            if(at[i] > at[j]) { 
                // Swap arrival time 
                temp = at[i]; at[i] = at[j]; at[j] = temp; 
                // Swap burst time 
                temp = bt[i]; bt[i] = bt[j]; bt[j] = temp; 
                // Swap process id 
                temp = pid[i]; pid[i] = pid[j]; pid[j] = temp; 
            } 
        } 
    } 
 
    // Calculate waiting time and turnaround time 
    wt[0] = 0; 
    int current_time = at[0]; 
 
    for(i = 0; i < n; i++) { 
        if(current_time < at[i]) 
            current_time = at[i]; // idle time 
        wt[i] = current_time - at[i]; 
        current_time += bt[i]; 
        tat[i] = wt[i] + bt[i]; 
        total_wt += wt[i]; 
        total_tat += tat[i]; 
    } 
 
    // Print results 
    printf("\nProcess\tAT\tBT\tWT\tTAT\n"); 
    for(i = 0; i < n; i++) { 
        printf("P%d\t%d\t%d\t%d\t%d\n", pid[i], at[i], bt[i], wt[i], 
tat[i]); 
    } 
 
    printf("\nAverage Waiting Time = %.2f\n", (float)total_wt/n); 
    printf("Average Turnaround Time = %.2f\n", (float)total_tat/n); 
 
    return 0; 
} 
 
 
### SJFC Non Preemtive 
 
#include <stdio.h> 
 
int main() { 
    int n, i, j; 
    int at[20], bt[20], wt[20], tat[20], pid[20], ct[20]; 
    int completed[20] = {0}; 
    int total_wt = 0, total_tat = 0; 
 
    printf("Enter number of processes: "); 
    scanf("%d", &n); 
 
    for(i = 0; i < n; i++) { 
        pid[i] = i+1; 
        printf("Enter Arrival Time for Process %d: ", pid[i]); 
        scanf("%d", &at[i]); 
        printf("Enter Burst Time for Process %d: ", pid[i]); 
        scanf("%d", &bt[i]); 
    } 
 
    int time = 0, completed_count = 0, shortest, min_bt; 
 
    while (completed_count < n) { 
        shortest = -1; 
        min_bt = 9999; 
 
        for(i = 0; i < n; i++) { 
            if(at[i] <= time && !completed[i] && bt[i] < min_bt) { 
                min_bt = bt[i]; 
                shortest = i; 
            } 
        } 
 
        if(shortest == -1) { 
            time++; 
        } else { 
            wt[shortest] = time - at[shortest]; 
            time += bt[shortest]; 
            tat[shortest] = time - at[shortest]; 
            ct[shortest] = time; 
            completed[shortest] = 1; 
            completed_count++; 
            total_wt += wt[shortest]; 
            total_tat += tat[shortest]; 
        } 
    } 
 
    printf("\nProcess\tAT\tBT\tCT\tWT\tTAT\n"); 
    for(i = 0; i < n; i++) { 
        printf("P%d\t%d\t%d\t%d\t%d\t%d\n", pid[i], at[i], bt[i], ct[i], 
wt[i], tat[i]); 
    } 
 
    printf("\nAverage Waiting Time = %.2f", (float)total_wt/n); 
    printf("\nAverage Turnaround Time = %.2f\n", (float)total_tat/n); 
 
    return 0; 
} 
 
 
### SJF Preemptive  
#include <stdio.h> 
 
int main() { 
    int n, i, time = 0, smallest; 
    int at[20], bt[20], rt[20], wt[20], tat[20], pid[20]; 
    int completed = 0, min_rt = 9999; 
    float total_wt = 0, total_tat = 0; 
 
    printf("Enter number of processes: "); 
    scanf("%d", &n); 
 
    for(i = 0; i < n; i++) { 
        pid[i] = i + 1; 
        printf("Enter Arrival Time for Process %d: ", pid[i]); 
        scanf("%d", &at[i]); 
        printf("Enter Burst Time for Process %d: ", pid[i]); 
        scanf("%d", &bt[i]); 
        rt[i] = bt[i]; // initialize remaining time 
    } 
 
    int complete[20] = {0}; 
    int end_time, start_time[20] = {0}; 
 
    while(completed != n) { 
        smallest = -1; 
        min_rt = 9999; 
 
        for(i = 0; i < n; i++) { 
            if(at[i] <= time && rt[i] > 0 && rt[i] < min_rt) { 
                min_rt = rt[i]; 
                smallest = i; 
            } 
        } 
 
        if(smallest == -1) { 
            time++; 
            continue; 
        } 
 
        rt[smallest]--; 
        time++; 
 
        if(rt[smallest] == 0) { 
            completed++; 
            end_time = time; 
            tat[smallest] = end_time - at[smallest]; 
            wt[smallest] = tat[smallest] - bt[smallest]; 
            total_wt += wt[smallest]; 
            total_tat += tat[smallest]; 
        } 
    } 
 
    printf("\nProcess\tAT\tBT\tWT\tTAT\n"); 
    for(i = 0; i < n; i++) { 
        printf("P%d\t%d\t%d\t%d\t%d\n", pid[i], at[i], bt[i], wt[i], 
tat[i]); 
    } 
 
    printf("\nAverage Waiting Time = %.2f", total_wt / n); 
    printf("\nAverage Turnaround Time = %.2f\n", total_tat / n); 
 
    return 0; 
} 
 
 
### Priority NON .p 
#include <stdio.h> 
 
int main() { 
    int n, i, j; 
    int at[20], bt[20], pr[20], pid[20]; 
    int wt[20], tat[20], ct[20], completed[20] = {0}; 
    int total_wt = 0, total_tat = 0; 
 
    printf("Enter number of processes: "); 
    scanf("%d", &n); 
 
    for(i = 0; i < n; i++) { 
        pid[i] = i + 1; 
        printf("Enter Arrival Time for Process P%d: ", pid[i]); 
        scanf("%d", &at[i]); 
        printf("Enter Burst Time for Process P%d: ", pid[i]); 
        scanf("%d", &bt[i]); 
        printf("Enter Priority for Process P%d (lower number = higher 
priority): ", pid[i]); 
        scanf("%d", &pr[i]); 
    } 
 
    int time = 0, count = 0; 
 
    while(count < n) { 
        int idx = -1; 
        int highest_priority = 9999; 
 
        for(i = 0; i < n; i++) { 
            if(at[i] <= time && !completed[i] && pr[i] < highest_priority) 
{ 
                highest_priority = pr[i]; 
                idx = i; 
            } 
        } 
 
        if(idx == -1) { 
            time++; 
        } else { 
            wt[idx] = time - at[idx]; 
            time += bt[idx]; 
            tat[idx] = time - at[idx]; 
            ct[idx] = time; 
            completed[idx] = 1; 
            count++; 
            total_wt += wt[idx]; 
            total_tat += tat[idx]; 
        } 
    } 
 
    printf("\nProcess\tAT\tBT\tPR\tCT\tWT\tTAT\n"); 
    for(i = 0; i < n; i++) { 
        printf("P%d\t%d\t%d\t%d\t%d\t%d\t%d\n", pid[i], at[i], bt[i], 
pr[i], ct[i], wt[i], tat[i]); 
    } 
 
    printf("\nAverage Waiting Time = %.2f", (float)total_wt / n); 
    printf("\nAverage Turnaround Time = %.2f\n", (float)total_tat / n); 
 
    return 0; 
} 
 
 
 
### Priority P 
#include <stdio.h> 
 
int main() { 
    int n, i; 
    int at[20], bt[20], pr[20], pid[20], rt[20]; 
    int wt[20], tat[20], completed = 0; 
    int time = 0, min_pr, idx; 
    float total_wt = 0, total_tat = 0; 
 
    printf("Enter number of processes: "); 
    scanf("%d", &n); 
 
    for(i = 0; i < n; i++) { 
        pid[i] = i + 1; 
        printf("Enter Arrival Time for Process P%d: ", pid[i]); 
        scanf("%d", &at[i]); 
        printf("Enter Burst Time for Process P%d: ", pid[i]); 
        scanf("%d", &bt[i]); 
        printf("Enter Priority for Process P%d (lower number = higher 
priority): ", pid[i]); 
        scanf("%d", &pr[i]); 
        rt[i] = bt[i]; // remaining time 
    } 
 
    int is_completed[20] = {0}; 
    int end_time; 
 
    while(completed < n) { 
        idx = -1; 
        min_pr = 9999; 
 
        for(i = 0; i < n; i++) { 
            if(at[i] <= time && rt[i] > 0 && pr[i] < min_pr) { 
                min_pr = pr[i]; 
                idx = i; 
            } 
        } 
 
        if(idx == -1) { 
            time++; 
            continue; 
        } 
 
        rt[idx]--; 
        time++; 
 
        if(rt[idx] == 0) { 
            completed++; 
            end_time = time; 
            tat[idx] = end_time - at[idx]; 
            wt[idx] = tat[idx] - bt[idx]; 
            total_wt += wt[idx]; 
            total_tat += tat[idx]; 
        } 
    } 
 
    printf("\nProcess\tAT\tBT\tPR\tWT\tTAT\n"); 
    for(i = 0; i < n; i++) { 
        printf("P%d\t%d\t%d\t%d\t%d\t%d\n", pid[i], at[i], bt[i], pr[i], 
wt[i], tat[i]); 
    } 
 
    printf("\nAverage Waiting Time = %.2f", total_wt / n); 
    printf("Average Turnaround Time = %.2f\n", total_tat / n); 
 
    return 0; 
} 
 
 
### Round Robin 
#include <stdio.h> 
 
int main() { 
    int n, i, time = 0, tq; 
    int at[20], bt[20], rt[20], wt[20], tat[20], ct[20], pid[20]; 
    int completed = 0; 
    float total_wt = 0, total_tat = 0; 
 
    printf("Enter number of processes: "); 
    scanf("%d", &n); 
 
    for(i = 0; i < n; i++) { 
        pid[i] = i + 1; 
        printf("Enter Arrival Time for Process P%d: ", pid[i]); 
        scanf("%d", &at[i]); 
        printf("Enter Burst Time for Process P%d: ", pid[i]); 
        scanf("%d", &bt[i]); 
        rt[i] = bt[i]; // remaining time 
    } 
 
    printf("Enter Time Quantum: "); 
    scanf("%d", &tq); 
 
    int queue[100], front = 0, rear = 0; 
    int visited[20] = {0}; 
    queue[rear++] = 0; // enqueue first process 
    visited[0] = 1; 
 
    while(completed < n) { 
        int idx = queue[front++]; 
        
        if(rt[idx] > tq) { 
            time += tq; 
            rt[idx] -= tq; 
        } else { 
            time += rt[idx]; 
            rt[idx] = 0; 
            completed++; 
            ct[idx] = time; 
            tat[idx] = ct[idx] - at[idx]; 
            wt[idx] = tat[idx] - bt[idx]; 
            total_wt += wt[idx]; 
            total_tat += tat[idx]; 
        } 
 
        // Enqueue newly arrived processes 
        for(i = 0; i < n; i++) { 
            if(i != idx && at[i] <= time && rt[i] > 0 && !visited[i]) { 
                queue[rear++] = i; 
                visited[i] = 1; 
            } 
        } 
 
        // Re-enqueue current process if not finished 
        if(rt[idx] > 0) { 
            queue[rear++] = idx; 
        } 
 
        // If queue is empty, push the next process with remaining time 
        if(front == rear) { 
            for(i = 0; i < n; i++) { 
                if(rt[i] > 0) { 
                    queue[rear++] = i; 
                    visited[i] = 1; 
                    break; 
                } 
            } 
        } 
    } 
 
    printf("\nProcess\tAT\tBT\tCT\tWT\tTAT\n"); 
    for(i = 0; i < n; i++) { 
        printf("P%d\t%d\t%d\t%d\t%d\t%d\n", pid[i], at[i], bt[i], ct[i], 
wt[i], tat[i]); 
    } 
 
    printf("\nAverage Waiting Time = %.2f", total_wt / n); 
    printf("\nAverage Turnaround Time = %.2f\n", total_tat / n); 
 
    return 0; 
} 
 
 
### Page replacement FCFS 
 
#include <stdio.h> 
 
int main() { 
    int pages[100], frames[10], n, f, i, j, k = 0, faults = 0, hit = 0, 
found; 
 
    printf("Enter number of pages: "); 
    scanf("%d", &n); 
 
    printf("Enter the page reference string:\n"); 
    for(i = 0; i < n; i++) { 
        scanf("%d", &pages[i]); 
    } 
 
    printf("Enter number of frames: "); 
    scanf("%d", &f); 
 
    // Initialize all frames to -1 (empty) 
    for(i = 0; i < f; i++) { 
        frames[i] = -1; 
    } 
 
    printf("\nPage\tFrames\t\tPage Fault\n"); 
 
    for(i = 0; i < n; i++) { 
        found = 0; 
 
        // Check if page is already in frame 
        for(j = 0; j < f; j++) { 
            if(frames[j] == pages[i]) { 
                found = 1; 
                hit++; 
                break; 
            } 
        } 
 
        if(!found) { 
            // Replace the oldest page using FIFO 
            frames[k] = pages[i]; 
            k = (k + 1) % f; // circular queue 
            faults++; 
        } 
 
        // Display frame contents 
        printf("%d\t", pages[i]); 
        for(j = 0; j < f; j++) { 
            if(frames[j] != -1) 
                printf("%d ", frames[j]); 
            else 
                printf("- "); 
        } 
        printf("\t%s\n", found ? "Hit" : "Miss"); 
    } 
 
    printf("\nTotal Page Faults: %d", faults); 
    printf("\nTotal Page Hits: %d\n", hit); 
 
    return 0; 
} 
 
 
### Optimal: 
 
#include <stdio.h> 
 
int findOptimal(int pages[], int frames[], int n, int f, int index) { 
    int farthest = index, pos = -1, i, j; 
    for(i = 0; i < f; i++) { 
        int found = 0; 
        for(j = index; j < n; j++) { 
            if(frames[i] == pages[j]) { 
                if(j > farthest) { 
                    farthest = j; 
                    pos = i; 
                } 
                found = 1; 
                break; 
            } 
        } 
        if(!found) return i; // Page not used again 
    } 
    return (pos == -1) ? 0 : pos; 
} 
 
int main() { 
    int pages[100], frames[10], n, f, i, j, faults = 0, hit = 0; 
    int found; 
 
    printf("Enter number of pages: "); 
    scanf("%d", &n); 
 
    printf("Enter the page reference string:\n"); 
    for(i = 0; i < n; i++) { 
        scanf("%d", &pages[i]); 
    } 
 
    printf("Enter number of frames: "); 
    scanf("%d", &f); 
 
    for(i = 0; i < f; i++) 
        frames[i] = -1; 
 
    printf("\nPage\tFrames\t\tPage Fault\n"); 
 
    for(i = 0; i < n; i++) { 
        found = 0; 
 
        for(j = 0; j < f; j++) { 
            if(frames[j] == pages[i]) { 
                found = 1; 
                hit++; 
                break; 
            } 
        } 
 
        if(!found) { 
            int pos = -1; 
            for(j = 0; j < f; j++) { 
                if(frames[j] == -1) { 
                    pos = j; 
                    break; 
                } 
            } 
            if(pos == -1) 
                pos = findOptimal(pages, frames, n, f, i+1); 
 
            frames[pos] = pages[i]; 
            faults++; 
        } 
 
        printf("%d\t", pages[i]); 
        for(j = 0; j < f; j++) { 
            if(frames[j] != -1) 
                printf("%d ", frames[j]); 
            else 
                printf("- "); 
        } 
        printf("\t%s\n", found ? "Hit" : "Miss"); 
    } 
 
    printf("\nTotal Page Faults: %d", faults); 
    printf("\nTotal Page Hits: %d\n", hit); 
 
    return 0; 
} 
 
 
### LRu 
#include <stdio.h> 
 
int findLRU(int time[], int f) { 
    int min = time[0], pos = 0; 
    for(int i = 1; i < f; i++) { 
        if(time[i] < min) { 
            min = time[i]; 
            pos = i; 
        } 
    } 
    return pos; 
} 
 
int main() { 
    int pages[100], frames[10], time[10], n, f; 
    int i, j, k, pos, faults = 0, counter = 0, hit = 0; 
    int found; 
 
    printf("Enter number of pages: "); 
    scanf("%d", &n); 
 
    printf("Enter the page reference string:\n"); 
    for(i = 0; i < n; i++) { 
        scanf("%d", &pages[i]); 
    } 
 
    printf("Enter number of frames: "); 
    scanf("%d", &f); 
 
    for(i = 0; i < f; i++) { 
        frames[i] = -1; 
        time[i] = 0; 
    } 
 
    printf("\nPage\tFrames\t\tPage Fault\n"); 
 
    for(i = 0; i < n; i++) { 
        found = 0; 
 
        for(j = 0; j < f; j++) { 
            if(frames[j] == pages[i]) { 
                counter++; 
                time[j] = counter; 
                found = 1; 
                hit++; 
                break; 
            } 
        } 
 
        if(!found) { 
            for(j = 0; j < f; j++) { 
                if(frames[j] == -1) { 
                    counter++; 
                    frames[j] = pages[i]; 
                    time[j] = counter; 
                    faults++; 
                    found = 1; 
                    break; 
                } 
            } 
        } 
 
        if(!found) { 
            pos = findLRU(time, f); 
            counter++; 
            frames[pos] = pages[i]; 
            time[pos] = counter; 
            faults++; 
        } 
 
        printf("%d\t", pages[i]); 
        for(j = 0; j < f; j++) { 
            if(frames[j] != -1) 
                printf("%d ", frames[j]); 
            else 
                printf("- "); 
        } 
        printf("\t%s\n", found ? "Hit" : "Miss"); 
    } 
 
    printf("\nTotal Page Faults: %d", faults); 
    printf("\nTotal Page Hits: %d\n", hit); 
 
    return 0; 
} 
 
 
### Partial allocation Best fit  
#include <stdio.h> 
 
int main() { 
    int b, p, i, j; 
    int blockSize[20], processSize[20], originalProcessSize[20]; 
    int allocated[20] = {0}; 
 
    printf("Enter number of memory blocks: "); 
    scanf("%d", &b); 
 
    printf("Enter size of each memory block:\n"); 
    for(i = 0; i < b; i++) { 
        printf("Block %d: ", i+1); 
        scanf("%d", &blockSize[i]); 
    } 
 
    printf("Enter number of processes: "); 
    scanf("%d", &p); 
 
    printf("Enter memory required by each process:\n"); 
    for(i = 0; i < p; i++) { 
        printf("Process %d: ", i+1); 
        scanf("%d", &processSize[i]); 
        originalProcessSize[i] = processSize[i]; 
    } 
 
    printf("\n--- Best-Fit Partial Allocation ---\n"); 
    printf("Process\tRequired\tAllocated\tBlock\n"); 
 
    for(i = 0; i < p; i++) { 
        int remaining = processSize[i]; 
 
        while(remaining > 0) { 
            int bestIdx = -1; 
 
            for(j = 0; j < b; j++) { 
                if(blockSize[j] >= remaining) { 
                    if(bestIdx == -1 || blockSize[j] < blockSize[bestIdx]) 
{ 
                        bestIdx = j; 
                    } 
                } 
            } 
 
            if(bestIdx != -1) { 
                // full allocation possible 
                printf("P%d\t%d\t\t%d\t\tB%d\n", i+1, 
originalProcessSize[i], remaining, bestIdx+1); 
                blockSize[bestIdx] -= remaining; 
                remaining = 0; 
            } else { 
                // try partial from largest available 
                int partialIdx = -1; 
                int maxBlock = -1; 
                for(j = 0; j < b; j++) { 
                    if(blockSize[j] > 0 && blockSize[j] < remaining) { 
                        if(blockSize[j] > maxBlock) { 
                            maxBlock = blockSize[j]; 
                            partialIdx = j; 
                        } 
                    } 
                } 
                if(partialIdx != -1) { 
                    printf("P%d\t%d\t\t%d (Partial)\tB%d\n", i+1, 
originalProcessSize[i], blockSize[partialIdx], partialIdx+1); 
                    remaining -= blockSize[partialIdx]; 
                    blockSize[partialIdx] = 0; 
                } else { 
                    printf("P%d\t%d\t\tNot Fully Allocated\n", i+1, 
originalProcessSize[i]); 
                    break; 
                } 
            } 
        } 
    } 
 
    return 0; 
} 
 
 
### Worst Fit and first 
#include <stdio.h> 
 
// Function to perform First Fit Partial Allocation 
void firstFit(int blockSize[], int b, int processSize[], int p) { 
    int originalProcessSize[20], i, j; 
    for(i = 0; i < p; i++) originalProcessSize[i] = processSize[i]; 
 
    int blocks[20]; 
    for(i = 0; i < b; i++) blocks[i] = blockSize[i]; 
 
    printf("\n--- First-Fit Partial Allocation ---\n"); 
    printf("Process\tRequired\tAllocated\tBlock\n"); 
 
    for(i = 0; i < p; i++) { 
        int remaining = processSize[i], done = 0; 
 
        for(j = 0; j < b && remaining > 0; j++) { 
            if(blocks[j] >= remaining) { 
                printf("P%d\t%d\t\t%d\t\tB%d\n", i+1, 
originalProcessSize[i], remaining, j+1); 
                blocks[j] -= remaining; 
                remaining = 0; 
                done = 1; 
                break; 
            } else if(blocks[j] > 0) { 
                printf("P%d\t%d\t\t%d (Partial)\tB%d\n", i+1, 
originalProcessSize[i], blocks[j], j+1); 
                remaining -= blocks[j]; 
                blocks[j] = 0; 
            } 
        } 
 
        if(remaining > 0 && !done) { 
            printf("P%d\t%d\t\tNot Fully Allocated\n", i+1, 
originalProcessSize[i]); 
        } 
    } 
} 
 
// Function to perform Worst Fit Partial Allocation 
void worstFit(int blockSize[], int b, int processSize[], int p) { 
    int originalProcessSize[20], i, j; 
    for(i = 0; i < p; i++) originalProcessSize[i] = processSize[i]; 
 
    int blocks[20]; 
    for(i = 0; i < b; i++) blocks[i] = blockSize[i]; 
 
    printf("\n--- Worst-Fit Partial Allocation ---\n"); 
    printf("Process\tRequired\tAllocated\tBlock\n"); 
 
    for(i = 0; i < p; i++) { 
        int remaining = processSize[i]; 
 
        while(remaining > 0) { 
            int worstIdx = -1; 
            int worstSize = -1; 
 
            for(j = 0; j < b; j++) { 
                if(blocks[j] >= remaining) { 
                    if(blocks[j] > worstSize) { 
                        worstSize = blocks[j]; 
                        worstIdx = j; 
                    } 
                } 
            } 
 
            if(worstIdx != -1) { 
                // Full allocation 
                printf("P%d\t%d\t\t%d\t\tB%d\n", i+1, 
originalProcessSize[i], remaining, worstIdx+1); 
                blocks[worstIdx] -= remaining; 
                break; 
            } else { 
                // Try partial allocation from largest available block 
                int partialIdx = -1; 
                worstSize = -1; 
                for(j = 0; j < b; j++) { 
                    if(blocks[j] > 0 && blocks[j] < remaining) { 
                        if(blocks[j] > worstSize) { 
                            worstSize = blocks[j]; 
                            partialIdx = j; 
                        } 
                    } 
                } 
 
                if(partialIdx != -1) { 
                    printf("P%d\t%d\t\t%d (Partial)\tB%d\n", i+1, 
originalProcessSize[i], blocks[partialIdx], partialIdx+1); 
                    remaining -= blocks[partialIdx]; 
                    blocks[partialIdx] = 0; 
                } else { 
                    printf("P%d\t%d\t\tNot Fully Allocated\n", i+1, 
originalProcessSize[i]); 
                    break; 
                } 
            } 
        } 
    } 
} 
 
int main() { 
    int blockSize[20], processSize[20]; 
    int b, p, i; 
 
    printf("Enter number of memory blocks: "); 
    scanf("%d", &b); 
    printf("Enter size of each memory block:\n"); 
    for(i = 0; i < b; i++) { 
        printf("Block %d: ", i+1); 
        scanf("%d", &blockSize[i]); 
    } 
 
    printf("Enter number of processes: "); 
    scanf("%d", &p); 
    printf("Enter memory required by each process:\n"); 
    for(i = 0; i < p; i++) { 
        printf("Process %d: ", i+1); 
        scanf("%d", &processSize[i]); 
    } 
 
    firstFit(blockSize, b, processSize, p); 
    worstFit(blockSize, b, processSize, p); 
 
    return 0; 
} 
 
 
 
### File allocation  
 
#include <stdio.h> 
#include <stdlib.h> 
 
#define MAX 50 
 
int memory[MAX]; // 0 = free, 1 = allocated 
 
// Function to reset memory 
void resetMemory() { 
    for (int i = 0; i < MAX; i++) { 
        memory[i] = 0; 
    } 
} 
 
// Sequential Allocation 
void sequentialAllocation() { 
    int start, length, i, flag = 0; 
 
    printf("\n--- Sequential Allocation ---\n"); 
    printf("Enter starting block and length of file: "); 
    scanf("%d %d", &start, &length); 
 
    if (start + length > MAX) { 
        printf("Memory exceeds limit!\n"); 
        return; 
    } 
 
    for (i = start; i < start + length; i++) { 
        if (memory[i] == 1) { 
            flag = 1; 
            break; 
        } 
    } 
 
    if (flag == 0) { 
        for (i = start; i < start + length; i++) { 
            memory[i] = 1; 
        } 
        printf("File allocated successfully in blocks: "); 
        for (i = start; i < start + length; i++) { 
            printf("%d ", i); 
        } 
        printf("\n"); 
    } else { 
        printf("Blocks already allocated. Try different blocks.\n"); 
    } 
} 
 
// Indexed Allocation 
void indexedAllocation() { 
    int index, n, i, block; 
 
    printf("\n--- Indexed Allocation ---\n"); 
    printf("Enter index block: "); 
    scanf("%d", &index); 
 
    if (memory[index] == 1) { 
        printf("Index block already allocated.\n"); 
        return; 
    } 
 
    printf("Enter number of blocks needed: "); 
    scanf("%d", &n); 
 
    int dataBlocks[n]; 
 
    printf("Enter block numbers:\n"); 
    for (i = 0; i < n; i++) { 
        scanf("%d", &block); 
        if (memory[block] == 1 || block == index) { 
            printf("Block %d already allocated or invalid. Try again.\n", 
block); 
            return; 
        } 
        dataBlocks[i] = block; 
    } 
 
    memory[index] = 1; 
    for (i = 0; i < n; i++) { 
        memory[dataBlocks[i]] = 1; 
    } 
 
    printf("File indexed at block %d and allocated blocks: ", index); 
    for (i = 0; i < n; i++) { 
        printf("%d ", dataBlocks[i]); 
    } 
    printf("\n"); 
} 
 
// Linked Allocation 
void linkedAllocation() { 
    int n, i, block; 
    int list[MAX], count = 0; 
 
    printf("\n--- Linked Allocation ---\n"); 
    printf("Enter number of blocks for the file: "); 
    scanf("%d", &n); 
 
    printf("Enter %d free block numbers:\n", n); 
    for (i = 0; i < n; i++) { 
        scanf("%d", &block); 
        if (memory[block] == 1 || block >= MAX) { 
            printf("Block %d is already allocated or invalid.\n", block); 
            return; 
        } 
        list[count++] = block; 
    } 
 
    for (i = 0; i < count; i++) { 
        memory[list[i]] = 1; 
    } 
 
    printf("Linked file allocated at blocks: "); 
    for (i = 0; i < count; i++) { 
        printf("%d -> ", list[i]); 
    } 
    printf("NULL\n"); 
} 
 
// Menu 
int main() { 
    int choice; 
 
    while (1) { 
        printf("\n\n--- File Allocation Menu ---\n"); 
        printf("1. Sequential Allocation\n"); 
        printf("2. Indexed Allocation\n"); 
        printf("3. Linked Allocation\n"); 
        printf("4. Reset Memory\n"); 
        printf("5. Exit\n"); 
        printf("Enter your choice: "); 
        scanf("%d", &choice); 
 
        switch (choice) { 
            case 1: 
                sequentialAllocation(); 
                break; 
            case 2: 
                indexedAllocation(); 
                break; 
            case 3: 
                linkedAllocation(); 
                break; 
            case 4: 
                resetMemory(); 
                printf("Memory reset successful.\n"); 
                break; 
            case 5: 
                exit(0); 
            default: 
                printf("Invalid choice.\n"); 
        } 
    } 
 
    return 0; 
} 
 
 
