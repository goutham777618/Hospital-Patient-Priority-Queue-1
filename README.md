
#include <stdio.h>
#include <string.h>

#define MAX 100

struct Patient {
    char name[30];
    int priority;
    int arrival;
};

struct Patient pq[MAX];
int size = 0;
int arrivalCount = 0;

// Swap two patients
void swap(struct Patient *a, struct Patient *b) {
    struct Patient temp = *a;
    *a = *b;
    *b = temp;
}

// Add a patient
void addPatient(char name[], int priority) {
    if (size == MAX) {
        printf("Queue is full!\n");
        return;
    }

    int i = size++;

    strcpy(pq[i].name, name);
    pq[i].priority = priority;
    pq[i].arrival = arrivalCount++;

    // Move patient upward
    while (i > 0) {
        int parent = (i - 1) / 2;

        if (pq[parent].priority < pq[i].priority ||
           (pq[parent].priority == pq[i].priority &&
            pq[parent].arrival < pq[i].arrival))
            break;

        swap(&pq[parent], &pq[i]);
        i = parent;
    }

    printf("%s added successfully.\n", name);
}

// Treat the next patient
void treatNextPatient() {
    if (size == 0) {
        printf("No patients waiting!\n");
        return;
    }

    printf("Treating patient: %s (Priority: %d)\n",
           pq[0].name, pq[0].priority);

    pq[0] = pq[size - 1];
    size--;

    // Restore min-heap property
    int i = 0;

    while (1) {
        int left = 2 * i + 1;
        int right = 2 * i + 2;
        int smallest = i;

        if (left < size &&
           (pq[left].priority < pq[smallest].priority ||
           (pq[left].priority == pq[smallest].priority &&
            pq[left].arrival < pq[smallest].arrival)))
            smallest = left;

        if (right < size &&
           (pq[right].priority < pq[smallest].priority ||
           (pq[right].priority == pq[smallest].priority &&
            pq[right].arrival < pq[smallest].arrival)))
            smallest = right;

        if (smallest == i)
            break;

        swap(&pq[i], &pq[smallest]);
        i = smallest;
    }
}

int main() {
    printf("HOSPITAL PATIENT PRIORITY QUEUE\n\n");

    // Add initial patients
    addPatient("P1", 3);
    addPatient("P2", 1);
    addPatient("P3", 2);
    addPatient("P4", 1);
    addPatient("P5", 3);
    addPatient("P6", 2);

    printf("\nTreatment Order:\n");

    while (size > 0) {
        treatNextPatient();
    }

    // Test adding a new emergency patient
    printf("\n--- New Emergency Test ---\n");

    addPatient("P1", 3);
    addPatient("P2", 1);
    addPatient("P3", 2);
    addPatient("P4", 1);
    addPatient("P5", 3);
    addPatient("P6", 2);

    printf("\nTreating first two patients:\n");
    treatNextPatient();
    treatNextPatient();

    printf("\nAdding new emergency patient:\n");
    addPatient("P7", 1);

    printf("\nRemaining Treatment Order:\n");

    while (size > 0) {
        treatNextPatient();
    }

    return 0;
}
