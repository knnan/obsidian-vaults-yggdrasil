[217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)
```python3
class Solution:

	
	def containsDuplicate(self,nums):
		pass
	def _using_xor(nums):
		result = 0
		for n in nums:
			resutl ^= n
		return result == 0
		
	
		
```