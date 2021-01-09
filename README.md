# JavaScript Interview Questions with Solutions

**Data Structures & Algorithm** | [JavaScript Fundamentals](https://github.com/oracleot/js-interview-prep-kit/tree/javascript-fundamentals)
## Data Structures & Algorithm

### Arrays
One of the most common data structures in programming. These questions are those involving arrays.


#### 1. Find the missing number in a given integer array of 1 to 100

Return: integer — the missing number in the array

My approach to this solution was to realize that the numbers are in consecutive order. Since the array is sorted, we can solve this in one pass by looking ahead using arr[i] + 1 and comparing that to arr[i + 1]. If arr[i + 1] is not equal to arr[i] + 1, it means that arr[i] + 1 is the missing number.

    'use strict'

    function findMissingNumber(arr) {
        // Note to self: anytime you find yourself checking arr[i+1], i must be less than arr.length - 1
        for(let i = 0; i < arr.length - 1; i++) {
            if(arr[i]+1 !== arr[i+1]) {
            return arr[i] + 1
            }
        }
    }

    const arr1 = [1,2,3,4,5,6,8,9,10]
    // findMissingNumber(arr1)

#### 2. Find a duplicate number in an array of integers

Return: integer — the duplicate number

Assumptions: if no duplicate is found, return false;
This problem can be easily solved using a hash. As we go through the array, we use the hash to keep track of which numbers we have seen before. If we encounter a number we have seen before, we return the number.

    'use strict'

    function findDuplicateNumber(arr) {
        let observedNumbers = {} // hash
        
        for(let i=0; i < arr.length; i++) {
            let num = arr[i]
            if(observedNumbers[num]) {
            return num
            } else {
            observedNumbers[num] = num
            }
        }
        return false
    }

    const arr2 = [1,2,3,4,5,6,7,8,8,6,10];
    // findDuplicateNumber(arr2)

#### 3. Find the largest and smallest number in an unsorted array of integers

Return: object — containing value of min and max

This can be solved by creating a min and max reference variable initialized to equal the value of the first item in the array — arr[0]. We then loop through the array and compare the values of min/max to arr[i] . If it is more or less, we update the value. Finally, I return an object with the values of min and max.

    'use strict'

    function findMaxMin(arr) {
        let min = arr[0]
        let max = arr[0]
        
        for(let i = 1; i < arr.length; i++) {
            let current = arr[i]
            if(current > max) {
            max = current
            } else if(current < min) {
            min = current
            }
        }
        
        return { "max": max, "min": min }
    }

    const arr3 = [6, 2, 3, 4, 100];
    // findMaxMin(arr3)

#### 4. Return an array showing the cumulative sum at each index of an array of integers

Return: array — integers showing the cumulative sum at each index

First we set a new array which will be initialized to contain the value at index 0 of list. This is because we need a starting reference value to begin our calculations. Then we loop through the list starting at index 1 (since we already have the value at list[0]. For each item in the array, we add the current value to the previous value and push it to the result array.

    'use strict'

    function cumulativeSum(arr) {
        let sum = [arr[0]]
        
        for(let i = 1; i < arr.length; i++) {
            let newSum = arr[i] + sum[sum.length - 1]
            sum.push(newSum)
        }
        return sum
    }
    
    const arr4 = [1,3,5,7];
    // cumulativeSum(arr4)

#### 5. Find all duplicate numbers in an array with multiple duplicates

Return: array — containing all duplicates found or empty array if none are found

Like question 2, I would use an hash map, but instead of setting the value of observed[arr[i]] = arr[i], we will increment a count. Depending on what we want to return, we can either return the object with the repeated values or we could push the duplicate numbers to a new array and return the new array. Since it is cleaner, and we can do it in one pass, I will do the latter.

    'use strict'

    function findMultipleDuplicates(arr) {
        let observedDuplicatesObj = {}
        let observedDuplicatesArr = []
        
        for(let i = 0; i < arr.length; i++) {
            let num = arr[i]
            if(observedDuplicatesObj[num]) {
            observedDuplicatesObj[num] += 1
            } else {
            observedDuplicatesObj[num] = 1
            }
        }
        
        for(let key in observedDuplicatesObj) {
            if(observedDuplicatesObj[key] > 1) {
            observedDuplicatesArr.push(Number(key))
            }
        }
        
        return observedDuplicatesArr
    }
    
    const arr5 = [1,1,2,3,4,5,6,7,8,6,6,7,7,7,10,10];
    // findMultipleDuplicates(arr5)

#### 6. Remove all duplicates from an array of integers

Return: array — without any duplicates

For this next solution, I got the idea from this blogpost which shows how to do this in Java. The concept is to do the opposite of what I did in solution 1 (looking 1 ahead), to keeping track of the one behind. By keeping track of the previous item encountered, we know whether or not we have encountered the number and whether or not to push to the result array.
Note: in order for this solution to work, we need a sorted array. In the Java example, they performed a .sort() method on the array before looping through it. For me, I will just assume that we are getting a sorted array as an input.

    'use strict'

    function removeAllDuplicates1(sortedarr) {
        let result = [];
        let previous = sortedarr[0];
        result[0] = previous;
        
        for(let i = 1; i < sortedarr.length; i++) {
            
            if (sortedarr[i] != previous) {
            result.push(sortedarr[i]);
            }
            
            previous = sortedarr[i];
        }
        
        return result;
    }
    
    const arr6 = [1, 1, 2, 2, 2, 3, 4, 4, 4, 5];
    // removeAllDuplicates1(arr6)

    function removeAllDuplicates2(unsortedarr) {
        let result = [];
        let uniqueArrObj = {};
        
        for(let i = 0; i < unsortedarr.length; i++) {
            let num = unsortedarr[i]
            
            if(uniqueArrObj[num]) {
            uniqueArrObj[num] += 1
            } else {
            uniqueArrObj[num] = 1
            }
        }
        for(let key in uniqueArrObj) {
            result.push(Number(key))
        }
        return result
    }
    
    const arr6_unsorted = [2, 1, 2, 4, 2, 3, 4, 6, 7, 5];
    // removeAllDuplicates2(arr6_unsorted)


#### 7. Find all pairs in an array of integers whose sum is equal to a given number

Return: array — matching pairs or empty arrays.

To simplify, the input array will not have duplicates. However, there will be negative and positive numbers. For the solution, like before, I will use a lookup hash. As you can see, knowing how to use a lookup hash, which has constant lookup time, can be very useful.

    'use strict'

    const findSumPairs = (arr, value) => {
        let sumsLookup = {};
        let output = [];
        
        for(let i = 0; i < arr.length; i++) {
            let targetVal = value - arr[i];
            
            if(sumsLookup[targetVal]) {
            output.push([arr[i], targetVal]);
            }  
            
            sumsLookup[arr[i]] = true;
        }
        console.log(arr)
        return output;
    }

    const arr7 = [1,5,6,1,0,1];
    // findSumPairs(arr7, 6)


### References

- https://medium.com/@shinjukudev/algorithm-interview-questions-and-answers-js-c44f0872b9b2
