

## Implementation

```python
class Node:
    def __init__(self,val=None,next=None):
        self.val = val
        self.next = next

class LinkedList:
    def __init__(self):
        self.head = None

    def insertBeginning(val):
        new_node = Node(val)
        new_node.next = self.head
        self.head = new_node

    def insertEnd(val):
        new_node = Node(val)
        if not self.head:
            self.head = new_node
            return

        cur = self.head
        while cur.next
            cur = cur.next
        cur.next = new_node
        
    def insertAfterVal(val):
        if not self.head:
            return False

        cur = self.head

        while cur:
            if cur.val == val:
                new_node = Node(val)
                new_node.next = cur.next
                cur.next = new_node
                return
            cur = cur.next

        return False
    
    def insertAfterValOrAppend(self,target,val):
        new_node = Node(val)
        # Case 1: empty list    
        if not self.head:
            self.head = new_node
            return

        # Case 2: Non empty list
        cur = self.head
        while cur:
            # Case 1: target found
            if cur.val == target:
                new_node.next = cur.next
                cur.next = new_node
                break;

            # Case 2: if target not found and its the last node
            if not cur.next:
                cur.next = new_node
                break
            cur = cur.next
    def detectCycle(self):
        if not self.head:
            return False

        slow = self.head
        fast = self.head
        
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next

            if slow == fast:
                return True
        return False


    def findStartOfCycle(self):
        if not self.head:
            return -1
        slow = self.head
        fast = self.head

        while fast and fast.next:
            
            slow = slow.next
            fast = fast.next.next

            if slow == fast:
                slow = self.head
                while slow != fast:
                    slow = slow.next
                    fast = fast.next
                return slow
        return -1








    def findVal(head):
       cur = self.head 
       while cur:
           if cur.val == val:
               return True,val
           cur = cur.next
       return False,-1

```