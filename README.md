# Set chiều cao động cho 1 element trong Vuejs

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Hôm nay mình sẽ trình bày cách chúng ta set động chiều cao cho 1 element phụ thuộc vào chiều cao của 1 element khác. Tức là chiều cao của element của chúng ta có thể thay đổi tự động được. Và mình tin chắc rằng đây là vấn đề chúng ta thường sẽ gặp phải khi làm giao diện với Vuejs

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Để đơn giản, giả sử chúng ta có 1 đoạn code dưới đây:

```
<div class="row">
  <div class="col-6 left">
    ...
  </div>
  <div class="col-6 right">
    ...
  </div>
</div>
```
      
Quy ước: cột bên phải tức là khối div có class right, cột bên trái là khối div có class left

# 1. Vấn đề:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Giả sử ta cột phải ta có rất nhiều element bên dưới nữa, sau khi Vuejs đổ data và render ra view. Và chiều cao của cột phải trên ta ko biết rõ là bao nhiêu (có thể 100px, 200px hay 350px … tùy thuộc vào data được đổ ra)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Tuy nhiên ta muốn cột bên trái --> phải có chiều cao bằng với cột bên phải. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Tới đây có thể vài bạn sẽ nghĩ chúng ta set 1 chiều cao chung cho cả 2 div thế là xong, tuy nhiên vì chiều cao của cột bên phải là ko cố định và nó thay đổi → khi render ra view nó sẽ bị lệch lên lệch xuống 1 chút. Như vậy, chiều cao 2 bên sẽ ko bằng khít với nhau được .Thứ 2 là, khi chiều cao cột bên phải thay đổi, thì chiều cao của cột bên trái cũng không tự thay đổi để bằng cột bên phải được vì chúng ta đã set cứng chiều cao cho cả 2 bên. Do đó chúng ta ko chơi kiểu set cứng height được :D

# 2. Ý tưởng:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Thay vì cách đó chúng ta sẽ lấy chiều cao của cột bên phải, sau đó gán cho cột bên trái. Và chúng ta đang dùng Vue, nên bất cứ khi nào chiều cao cột phải thay đổi thì chiều cao của cột trái tự động cx sẽ thay đổi theo - và chiều cao 2 bên sẽ bằng khít nhau luôn.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Hehe luyên thuyên nhiều rồi, nói thì dễ lắm nhưng khi làm sẽ có nhiều vấn đề đấy :v giờ mình sẽ triển khai luôn nhá :D

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Đầu tiền, ta phải dùng 1 biến trong data () để lưu giữ chiều cao cột phải

```
  data () {
    return {
         heightRight: Number
    }
  },
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Muốn lấy chiều cao cột bên phải ta phải dùng 1 thuộc tính ref trong Vue như sau 

```
  <div class="col-6" ref="coverRight">
```
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; sau đó trong Vue mình sẽ lấy chiều cao của cột phải và gán cho heightRight như sau 

```
  this.heightRight = this.$refs.coverRight.clientHeight
```


```
  this.$nextTick(() => {
      this.heightRight = this.$refs.coverRight.clientHeight
  })
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Các bạn chú ý, tại sao phải dùng nextTick ở đây ???

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Nếu ko dùng nextTick() thì khi chiều cao cột phải thay đổi, chiều cao bạn lấy về sẽ là chiều cao cột phải cũ, nhưng mà ta muốn nó lấy chiều cao mới tức là chiều cao đã được update khi ta thay đổi data cơ mà. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Như vậy nextTick ở đây sẽ giải quyết vấn đề đó, khi có sự thay đổi data thì nextTick sẽ trì hoãn việc thực thi callback bên trong nó, cho tới khi nào DOM được update xong thì nó sẽ thực thi callback.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Với việc sử dụng nextTick ta đã có thể lấy được chiều cao sau khi update rồi đó

Tiếp theo, trong <template> chúng ta sẽ binding 1 style height cho cột trái để nó nhận được giá trị heightRight ta vừa set như sau

```
  <div class="col-6 left" :style="{'height': heightRight  + 'px'}">
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Như vậy là cột trái đã sẵn sàng nhận giá trị chiều cao của cột phải rồi

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Trong code của mình, khi nào data của cột phải thay đổi ⇔ chiều cao cột phải thay đổi ta chỉ cần gọi đoạn code nextTick ở trên là xong đúng ko.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Ko hẳn như vậy sẽ có 1 số vấn đề như sau: 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Sẽ có rất nhiều chỗ có thể dẫn đến data của cột phải thay đổi → chẳng lẽ ta thêm đoạn code này vào tất cả các chỗ trên. Rất rối và ta ko control được khi app to ra 


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Ở đây chúng ta có 1 cách xuất sắc hơn đó là ta viết đoạn code này vào giai đoạn updated trong lifecycle của Vue - Đó là lúc mà Vue đã render xong giao diện khi có bất kì sự thay đổi dữ liệu nào xảy ra

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Trước hết, ta phải gói đoạn code nextTick vào trong 1 function và đưa vào trong method trước như sau:

```
methods: {
    ...
    setHeightLeft () {
      this.$nextTick(() => {
        this.heightListRemind = this.$refs.coverRight.clientHeight
      })
    },
    ...
}
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Sau đó trong updated() ta chỉ cần gọi hàm setHeightLeft là xong

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Hơi loằng ngoằng 1 chút nhưng cũng khá đơn giản thôi phải không nào, chúc các bạn thành công, hẹn gặp lại các bạn :)))
































