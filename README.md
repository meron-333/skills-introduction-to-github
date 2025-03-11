
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class MemoryAllocator {

    private int totalMemorySize;
    private int[] memoryMap; // Represents the memory blocks

    public MemoryAllocator(int totalMemorySize) {
        this.totalMemorySize = totalMemorySize;
        this.memoryMap = new int[totalMemorySize];
        Arrays.fill(memoryMap, 0); // Initialize memory as free
    }

    public void allocate(int processSize, String allocationStrategy) {
        if (allocationStrategy.equals("first-fit")) {
            firstFit(processSize);
        } else if (allocationStrategy.equals("best-fit")) {
            bestFit(processSize);
        } else if (allocationStrategy.equals("worst-fit")) {
            worstFit(processSize);
        } else {
            System.err.println("Invalid allocation strategy: " + allocationStrategy);
        }
    }

    private void firstFit(int processSize) {
        int startIndex = 0;
        for (int i = 0; i < totalMemorySize; i++) {
            if (memoryMap[i] == 0) { // Found a free block
                if (i + processSize <= totalMemorySize) {
                    // Allocate the memory
                    for (int j = i; j < i + processSize; j++) {
                        memoryMap[j] = processSize;
                    }
                    System.out.println("Allocated " + processSize + " units at index " + i + " using First Fit.");
                    return; // Allocation successful
                } 
                else {
                    startIndex = i + 1; // Start search from the next block
                }
            }
        }
        System.out.println("F2ailed to allocate " + processSize + " units using First Fit.");
    }

    private void bestFit(int processSize) {
        int bestFitIndex = -1;
        int bestFitSize = totalMemorySize + 1; // Initial best fit size is larger than memory
        for (int i = 0; i < totalMemorySize - processSize + 1; i++) {
            if (isFreeBlock(i, processSize)) {
                int currentBlockSize = getBlockSize(i, processSize);
                if (currentBlockSize < bestFitSize) {
                    bestFitIndex = i;
                    bestFitSize = currentBlockSize;
                }
            }
        }

        if (bestFitIndex != -1) {
            // Allocate the memory
            for (int j = bestFitIndex; j < bestFitIndex + processSize; j++) {
                memoryMap[j] = processSize;
            }
            System.out.println("Allocated " + processSize + " units at index " + bestFitIndex + " using Best Fit.");
        } else {
            System.out.println("Failed to allocate " + processSize + " units using Best Fit.");
        }
    }

    private void worstFit(int processSize) {
        int worstFitIndex = -1;
        int worstFitSize = 0; // Initial worst fit size is 0

        for (int i = 0; i < totalMemorySize - processSize + 1; i++) {
            if (isFreeBlock(i, processSize)) {
                int currentBlockSize = getBlockSize(i, processSize);
                if (currentBlockSize > worstFitSize) {
                    worstFitIndex = i;
                    worstFitSize = currentBlockSize;
                }
            }
        }

        if (worstFitIndex != -1) {
            // Allocate the memory
            for (int j = worstFitIndex; j < worstFitIndex + processSize; j++) {
                memoryMap[j] = processSize;
            }
            System.out.println("Allocated " + processSize + " units at index " + worstFitIndex + " using Worst Fit.");
        } else {
            System.out.println("Failed to allocate " + processSize + " units using Worst Fit.");
        }
    }

    private boolean isFreeBlock(int startIndex, int processSize) {
        for (int i = startIndex; i < startIndex + processSize; i++) {
            if (memoryMap[i] != 0) {
                return false; // Block is not completely free
            }
        }
        return true; // Block is completely free
    }

    private int getBlockSize(int startIndex, int processSize) {
        int blockSize = 0;
        for (int i = startIndex; i < startIndex + processSize; i++) {
            if (memoryMap[i] == 0) {
                blockSize++;
            }
        }
        return blockSize;
    }

    public void printMemoryMap() {
        System.out.print("Memory Map: ");
        for (int i = 0; i < totalMemorySize; i++) {
            System.out.print(memoryMap[i] + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        int totalMemory = 10;
        MemoryAllocator allocator = new MemoryAllocator(totalMemory);

        System.out.println("----------------------");
        System.out.println("First Fit:");
        System.out.println("----------------------");
        allocator.printMemoryMap();

        List<Integer> processSizes = Arrays.asList(2, 4, 3, 1, 5);
        for (int processSize : processSizes) {
            allocator.allocate(processSize, "first-fit");
            allocator.printMemoryMap();
        }

        System.out.println("----------------------");
        System.out.println("Best Fit:");
        System.out.println("----------------------");
        allocator = new MemoryAllocator(totalMemory); // Reset the allocator
        allocator.printMemoryMap();
        for (int processSize : processSizes) {
            allocator.allocate(processSize, "best-fit");
            allocator.printMemoryMap();
        }

        System.out.println("----------------------");
        System.out.println("Worst Fit:");
        System.out.println("----------------------");
        allocator = new MemoryAllocator(totalMemory); // Reset the allocator
        allocator.printMemoryMap();
        for (int processSize : processSizes) {
            allocator.allocate(processSize, "worst-fit");
            allocator.printMemoryMap();
        }
    }
}

