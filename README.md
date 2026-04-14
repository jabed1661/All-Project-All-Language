FCFS

#include <stdio.h>

int main()
{
    int pid[50], bt[50], at[50];
    int st[50], ct[50], wt[50], tat[50];
    int n, i, j;

   printf("Enter the number of processes: ");
    scanf("%d",&n);

    printf("Enter process IDs:\n");
    for (i = 0; i < n; i++) scanf("%d", &pid[i]);

    printf("Enter burst times:\n");
    for (i = 0; i < n; i++) scanf("%d", &bt[i]);

    printf("Enter arrival times:\n");
    for (i = 0; i < n; i++) scanf("%d", &at[i]);

    
    for (i = 0; i < n - 1; i++) {
        int min_idx = i;
        for (j = i + 1; j < n; j++) {
            if (at[j] < at[min_idx]) min_idx = j;
        }
       
           to preserve relative order (stable). */
        if (min_idx != i) {
            int tmp_at = at[min_idx];
            at[min_idx]=at[i];
            at[i] = tmp_at;

            int tmp_bt = bt[min_idx];
            bt[min_idx]=bt[i];
            bt[i] = tmp_bt;

            int tmp_pid = pid[min_idx];
            pid[min_idx]=pid[i];
            pid[i] = tmp_pid;
        }
    }

   
    st[0] = at[0];
    ct[0] = st[0] + bt[0];
    //wt[0] = st[0] - at[0];      
    tat[0] = ct[0] - at[0];
    wt[0]=tat[0]-bt[0];

    for (i = 1; i < n; i++) {
       
        if (ct[i-1] <= at[i]) {
            st[i] = at[i];
        } else {
           
            st[i] = ct[i-1];
        }
        ct[i] = st[i] + bt[i];
        
        tat[i] = ct[i] - at[i];
        wt[i]=tat[i]-bt[i];
    }

    float total_wt = 0.0f, total_tat = 0.0f;
    printf("\nPID\tAT\tBT\tST\tCT\tWT\tTAT\n");
    for (i = 0; i < n; i++) {
        printf("%d\t%d\t%d\t%d\t%d\t%d\t%d\n", pid[i], at[i], bt[i], st[i], ct[i], wt[i], tat[i]);
        total_wt += wt[i];
        total_tat += tat[i];
    }

    printf("\nAverage Waiting Time = %.2f\n", total_wt / n);
    printf("Average Turnaround Time = %.2f\n", total_tat / n);

    return 0;
}


SJF

#include <stdio.h>

int main() {
    int pid[20], bt[20], at[20], wt[20], tat[20], ct[20];
    int n, completed = 0, time = 0, i, smallest;
    int visited[20] = {0};
    float avgWT = 0, avgTAT = 0;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    printf("Enter Process IDs: ");
    for (i = 0; i < n; i++)
        scanf("%d", &pid[i]);

    printf("Enter Burst Times: ");
    for (i = 0; i < n; i++)
        scanf("%d", &bt[i]);

    printf("Enter Arrival Times: ");
    for (i = 0; i < n; i++)
        scanf("%d", &at[i]);

    while (completed != n) {
        smallest = -1;

        // Find process with shortest burst time among arrived ones
        for (i = 0; i < n; i++) {
            if (!visited[i] && at[i] <= time) {
                if (smallest == -1 || bt[i] < bt[smallest]) {
                    smallest = i;
                }
            }
        }

      
        if (smallest == -1) {
            time++;
            continue;
        }

        
        time += bt[smallest];
        ct[smallest] = time;
        tat[smallest] = ct[smallest] - at[smallest];
        wt[smallest] = tat[smallest] - bt[smallest];

        visited[smallest] = 1;
        completed++;
    }

    printf("\nPID\tAT\tBT\tCT\tTAT\tWT\n");
    for (i = 0; i < n; i++) {
        printf("%d\t%d\t%d\t%d\t%d\t%d\n",
               pid[i], at[i], bt[i], ct[i], tat[i], wt[i]);

        avgWT += wt[i];
        avgTAT += tat[i];
    }

    avgWT /= n;
    avgTAT /= n;

    printf("\nAverage Waiting Time: %.2f", avgWT);
    printf("\nAverage Turnaround Time: %.2f\n", avgTAT);

    return 0;
}

Piority

#include <stdio.h>

int main() {
    int n, i;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    int pid[n], at[n], bt[n], pr[n];
    int wt[n], tat[n], completed[n];

    
    for (i = 0; i < n; i++) {
        pid[i] = i + 1;
        printf("\nProcess %d:\n", pid[i]);

        printf("Arrival Time: ");
        scanf("%d", &at[i]);

        printf("Burst Time: ");
        scanf("%d", &bt[i]);

        printf("Priority (lower value = higher priority): ");
        scanf("%d", &pr[i]);

        completed[i] = 0;
    }

    int time = 0, done = 0;
    float total_wt = 0, total_tat = 0;

    while (done < n) {
        int idx = -1;            
        int highest_priority = 9999;       

     
        for (i = 0; i < n; i++) {
            if (at[i] <= time && completed[i] == 0) {
                if (pr[i] < highest_priority) {
                    highest_priority = pr[i];
                    idx = i;
                }
                else if (pr[i] == highest_priority) {        
                    if (at[i] < at[idx]) {
                        idx = i;
                    }
                }
            }
        }

        if (idx != -1) {            
            time += bt[idx];

            tat[idx] = time - at[idx];
            wt[idx] = tat[idx] - bt[idx];

            total_wt += wt[idx];
            total_tat += tat[idx];

            completed[idx] = 1;
            done++;
        } else {
            time++; 
        }
    }


    printf("\nPID\tAT\tBT\tPR\tWT\tTAT\n");
    for (i = 0; i < n; i++) {
        printf("%d\t%d\t%d\t%d\t%d\t%d\n",
               pid[i], at[i], bt[i], pr[i], wt[i], tat[i]);
    }

    printf("\nAverage Waiting Time = %.2f", total_wt / n);
    printf("\nAverage Turnaround Time = %.2f\n", total_tat / n);

    return 0;
}

Round Robin

#include <stdio.h>

int main() {
    int n, tq;
    int at[10], bt[10], rt[10], ct[10], tat[10], wt[10];
    int queue[100], front = 0, rear = 0;
    int time = 0, completed = 0;
    int i, j;
    int visited[10] = {0};
    int done[10] = {0};

    printf("Enter number of processes: ");
    scanf("%d", &n);

    for (i = 0; i < n; i++) {
        printf("Enter Arrival Time and Burst Time for Process %d: ", i + 1);
        scanf("%d%d", &at[i], &bt[i]);
        rt[i] = bt[i];
    }

    printf("Enter Time Quantum: ");
    scanf("%d", &tq);

    int min_at = at[0], first = 0;
    for (i = 1; i < n; i++) {
        if (at[i] < min_at) {
            min_at = at[i];
            first = i;
        }
    }

    time = at[first];
    queue[rear++] = first;
    visited[first] = 1;

    printf("\nGantt Chart:\n|");

    while (completed < n) {
        if (front == rear) {
            
            for (i = 0; i < n; i++) {
                if (done[i] == 0) {
                    queue[rear++] = i;
                    visited[i] = 1;
                    time = at[i];    
                    break;
                }
            }
        }

        i = queue[front++]; 

        if (rt[i] > tq) {
            printf(" P%d |", i + 1);
            time += tq;
            rt[i]= rt[i]-tq;
        } else if (rt[i] > 0) {
            printf(" P%d |", i + 1);
            time += rt[i];
            ct[i] = time;
            tat[i] = ct[i] - at[i];
            wt[i] = tat[i] - bt[i];
            rt[i] = 0;
            done[i] = 1;
            completed++;
        }

       
        for (j = 0; j < n; j++) {
            if (at[j] <= time && rt[j] > 0 && visited[j] == 0) {
                queue[rear++] = j;
                visited[j] = 1;
            }
        }

        
        if (rt[i] > 0) {
            queue[rear++] = i;
        }
    }

    
    float avgWT = 0, avgTAT = 0;
    printf("\n\nProcess\tAT\tBT\tCT\tTAT\tWT\n");
    for (i = 0; i < n; i++) {
        avgWT += wt[i];
        avgTAT += tat[i];
        printf("P%d\t%d\t%d\t%d\t%d\t%d\n", i + 1, at[i], bt[i], ct[i], tat[i], wt[i]);
    }

    printf("\nAverage Waiting Time = %.2f", avgWT / n);
    printf("\nAverage Turnaround Time = %.2f\n", avgTAT / n);

    return 0;
}


FIFO

#include <stdio.h>

int main() {
    int frames, pages[50], temp[50];
    int i, j, k, n, page_faults = 0, flag;

    printf("Enter number of frames: ");
    scanf("%d", &frames);

    printf("Enter number of pages: ");
    scanf("%d", &n);

    printf("Enter the page reference string:\n");
    for(i = 0; i < n; i++)
        scanf("%d", &pages[i]);

    for(i = 0; i < frames; i++)  
        temp[i] = -1;

    j = 0; 

    printf("\nPage\tFrames\n");

    for(i = 0; i < n; i++) {           
        flag = 0;

      
        for(k = 0; k < frames; k++) {
            if(temp[k] == pages[i]) {
                flag = 1;
                break;
            }
        }

       
        if(flag == 0) {
            temp[j] = pages[i];    
            j = (j + 1) % frames;
            page_faults++;
        }

        printf("%d\t", pages[i]);
        for(k = 0; k < frames; k++) {
            if(temp[k] != -1)
                printf("%d ", temp[k]);
            else
                printf("- ");
        }
        printf("\n");
    }

    printf("\nTotal Page Faults = %d\n", page_faults);

    return 0;
}

Bankers ALgo

#include <stdio.h>

int main() {
    int n, m;  
    int allocation[10][10], max[10][10], need[10][10], available[10];
    int finish[10] = {0}, safeSequence[10];
    int i, j, k, count = 0;

    
    printf("Enter number of processes: ");
    scanf("%d", &n);

    printf("Enter number of resource types: ");
    scanf("%d", &m);

    
    printf("Enter Allocation Matrix (%d x %d):\n", n, m);
    for (i = 0; i < n; i++)
        for (j = 0; j < m; j++)
            scanf("%d", &allocation[i][j]);


    printf("Enter Max Matrix (%d x %d):\n", n, m);
    for (i = 0; i < n; i++)
        for (j = 0; j < m; j++)
            scanf("%d", &max[i][j]);

  
    printf("Enter Available Resources (%d):\n", m);
    for (i = 0; i < m; i++)
        scanf("%d", &available[i]);

    for (i = 0; i < n; i++)
        for (j = 0; j < m; j++)
            need[i][j] = max[i][j] - allocation[i][j];

   
    while (count < n) {
        int found = 0;

        for (i = 0; i < n; i++) {
            if (finish[i] == 0) {  
                int canRun = 1;

                for (j = 0; j < m; j++) {
                    if (need[i][j] > available[j]) {
                        canRun = 0;  
                        break;
                    }
                }

                if (canRun) {
                
                    for (k = 0; k < m; k++)
                        available[k] += allocation[i][k];

                    safeSequence[count++] = i;
                    finish[i] = 1;
                    found = 1;
                }
            }
        }

        if (!found) {
            printf("System is NOT in safe state.\n");
            return 0;
        }
    }

   
    printf("System is in SAFE state.\nSafe Sequence: ");
    for (i = 0; i < n; i++)
        printf("P%d ", safeSequence[i]);

    printf("\n");
    return 0;
}




