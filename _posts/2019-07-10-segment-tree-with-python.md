---
title: Segment Tree with Python
tag: data-structure
mathjax: true
---

## Bài toán
Cho dãy có $$N$$ phần tử ($$N \le 10^5$$). Ban đầu, các phần tử có giá trị là 0.  
Có $$Q$$ truy vấn ($$Q \le 10^5$$). Mỗi truy vấn thuộc một trong 2 dạng:
1. Gán giá trị *v* cho phần tử thứ *i* trong mảng
2. Tìm giá trị lớn nhất trong khoảng *[u,v]* của mảng

Nếu chỉ dùng cấu trúc mảng bình thường thì truy vấn loại (1) mất O(1) trong khi truy vấn loại 2 mất O(N), do đó không thể đáp ứng được tất cả các truy vấn trong thời gian cho phép. Với các bài toán có dạng này, nên sử dụng Segment Tree

## Segment Tree
Để hiểu chi tiết về Segment Tree, tham khảo bài viết này: [Tất tần tật về Cây Phân Đoạn (Segment Tree)](https://vnoi.info/wiki/algo/data-structures/segment-tree-extend). Trong bài này chỉ là tóm tắt của bài viết trên và implement bằng Python thay vì C/C++. Để đơn giản việc implement và thống nhất với bài viết trên VNOI, các mảng sẽ bắt đầu từ 1 thay vì 0.  

Segment Tree (cây phân đoạn) là một cây nhị phân đầy đủ, được sử dụng cùng mới một mảng, mỗi node trên Segment Tree sẽ lưu giữ thông tin về một khoảng nào đó trong mảng. Với mảng có N phần tử, root node sẽ lưu trữ thông tin trong khoảng **[1, N]** của mảng đó, nút con trái của root node sẽ lưu trữ thông tin trong khoảng **[1, N/2]** và nút con trái sẽ giữ thông tin của khoảng **[N/2+1, N]** của mảng.  

Tổng quát: nếu nút có id **node_id** lưu trữ thông tin của khoảng **[left, right]** thì
* nút con trái (**node_id\*2**) sẽ lưu trữ thông tin của khoảng **[left, (left+right)/2]**  
* nút con phải (**node_id\*2+1**) sẽ lưu trữ thông tin của khoảng **[(left+right)/2+1, right]**  

![Segment Tree với mảng 7 phần tử](https://vnoi.info/wiki/uploads/segment_tree_structure_example.png)

Với Segment Tree, thông thường các thông tin được lưu trên các node thường là các giá trị như:
* min của một khoảng
* max của một khoảng
* avg của một khoảng
* count của một gía trị cụ thể trong một khoảng 
* ngoài ra có thể sử dụng các phép toán khác để phù hợp với bài toán (tuy nhiên phải phù hợp với tính chất của Segment Tree)

Tính chất quan trọng nhất của Segment Tree đó là tính đệ quy:  
$$node = op(node_{left}, node_{right})$$  
Với *op* là các phép toán như min, max, avg,... như trên.

Với Segment Tree, độ phức tạp của 2 loại query đều là *O(logN)*  

Để lưu trữ Segment Tree, có thể dùng mảng 1 chiều với kích thước *4N*  

## Code
Đây là code implement của Max Segment Tree với Python 3.  
Để phù hợp với từng bài toán chỉ cần sửa phép toán lấy max, cấu trúc dữ liệu lưu trữ trên các node, và giá trị trả về khi *[u..v]* nằm ngoài khoảng lưu trữ của node

```python
class MaxSegmentTree:
    def __init__(self, max_size, init_value=0):
        self.lenght = max_size
        # mảng bắt đầu từ 1 nên max_size+1 để padding phần tử đầu tiên
        max_size += 1
        self.arr = [init_value]*max_size
        self.tree = [init_value]*(max_size*4)

    def __set(self, ix, value, node_id, left, right):
        """Cập nhật giá trị arr[ix] = value
        Parameters:
            - ix: chỉ số của arr cần gán giá trị value
            - value: giá trị gán
            - node_id: index của node trên segment tree, node này quản lí giá trị max(arr[left..right])
            - left, right: khoảng giá trị mà node_id quản lí
        """
        # ix  nằm ngoài đoạn left ... right
        if ix < left or ix > right:
            return

        # node id là nút lá, chỉ giữ 1 giá trị thì gán giá trị đó cho node
        if left == right:
            self.tree[node_id] = value
            self.arr[ix] = value
            return

        # gọi đệ quy để gán
        mid = (left+right)//2
        self.__set(ix, value, node_id*2, left, mid)
        self.__set(ix, value, node_id*2+1, mid+1, right)

        # cập nhật giá trị sau khi gán
        self.tree[node_id] = max(self.tree[node_id*2], self.tree[node_id*2+1])


    def __get(self, u, v, node_id, left, right):
        """Trả về giá trị max trong arr[u..v]
        Parameters:
            - u, v: khoảng cần truy vấn trên arr, ở đây là max(arr[u..v])
            - node_id, left, right: chỉ số node và khoảng mà node đó lưu trữ thông tin
        """
        # u..v nằm ngoài khoảng left..right
        if v < left or u > right:
            return -float("inf")

        # left..right nằm trong khoảng u..v: u<=left<=right<=v
        if u<=left and right<=v:
            return self.tree[node_id]

        mid = (left+right)//2
        v_left = self.__get(u,v,node_id*2, left, mid)
        v_right = self.__get(u,v,node_id*2+1, mid+1, right)
        return max(v_left, v_right)

    def set(self, ix, value):
        self.__set(ix, value, 1, 1, self.lenght)

    def get(self, u, v):
        return self.__get(u, v, 1, 1, self.lenght)

    def get_arr(self):
        return self.arr[1:]

    def get_value(self, ix):
        return self.arr[ix]

```