# Set chiều cao động cho 1 element trong Vuejs

# 1. Equal column là gì ?
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Trước hết, **equal column** hay **đồng nhất column** là như thế nào. Ví dụ, trong css ta làm thế nào để 2 column trong hệ thống grid của bootstrap có chiều cao luôn bằng nhau, khi mà content của 1 trong 2 column dài ra thì height của column đó cũng bị dài theo. Nếu ta không xử lý gì thì column nào có height của content lớn hơn thì column đó sẽ height lớn hơn column còn lại. Đây là điều thường xuyên chúng ta gặp phải khi thiết kế giao diện với bootstrap

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Trong css, để cân bằng được 2 column như thế bất kể nội dung bên trong 2 column có chiều cao bao nhiêu, mình xin chia sẻ có 3 cách để thực hiện:

**Cách 1: Làm cho các thẻ div hoạt động giống như 1 table**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Table ko chỉ ứng dụng để thiết kể bảng biểu mà nó còn để căn đều các column bên trong nó. Vì các cell trong table đều có kích thước giống nhau đúng ko nào. Ví dụ ta có đoạn code như sau:

```
<div id="container">
	<div id="row">

		<div id="left">
			<h4>Left Col</h4>
			<p>...</p>
		</div>

		<div id="middle">
			<h4>Middle Col</h4>
			<p>...</p>
		</div>

		<div id="right">
			<h4>Right Col</h4>
			<p>...</p>
		</div>
	</div>
</div>
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Để làm cho các thẻ div hoạt động giống table ta sẽ sử dụng thuộc tính display với 3 giá trị là: table, table-row và table-cell như sau

```
  #container {
    display: table;
    }

  #row  {
    display: table-row;
    }

  #left, #right, #middle {
    display: table-cell;
    }
```

**Cách 2: Sử dụng Negative Margin và Huge Padding Trick**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Cách tiếp cận này thực sự, thực sự tuyệt vời và có lẽ là giải pháp tốt nhất cho hầu hết mọi người. Mình cx không biết ai đã nghĩ ra nó, nhưng nó siêu sáng tạo và có nhiều lợi ích:

* Có reponsive luôn mà ko phải suy nghĩ nhiều
* Tốn ít effort hơn để đảm bảo nó hoạt động tốt trong các tình huống khác nhau
* Làm việc trên tất cả các column bất kể kích thước của chúng

```
<div class="row match-my-cols">
    <div class="col-sm-4">...</div>
    <div class="col-sm-4">...</div>
    <div class="col-sm-4">...</div>
</div>
```

```
.row.match-my-cols {
    overflow: hidden; 
}

.row.match-my-cols [class*="col-"]{
    margin-bottom: -99999px;
    padding-bottom: 99999px;
}
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Bản chất cách hoạt động là: Nó thêm 99999px height vào column thông qua padding và sau đó sử dụng negative margin để buộc vị trí như thể nó không có ở đó. Sau đó, .row chỉ che giấu bất cứ điều gì bị tràn.

**Cách 3: Sử dụng các thuộc tính của flex-box**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Flex-box là công cụ rất tuyệt vời để căn chỉnh giao diện theo ý mình mong muốn mà ko cần quan tâm đến width và height của các phần tử. Ngoài ra, nó còn rất dễ sử dụng nữa

```
<div class="row is-flex">
    <div class="col-sm-4">...</div>
    <div class="col-sm-4">...</div>
    <div class="col-sm-4">...</div>
</div>
```

```
.row.is-flex {
    display: flex;
    flex-wrap: wrap;
}
.row.is-flex > [class*='col-'] {
    display: flex;
    flex-direction: column;
}

/*
* And with max cross-browser enabled.
* Nobody should ever write this by hand. 
* Use a preprocesser with autoprefixing.
*/
.row.is-flex {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    -webkit-flex-wrap: wrap;
    -ms-flex-wrap: wrap;
    flex-wrap: wrap;
}

.row.is-flex > [class*='col-'] {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-orient: vertical;
    -webkit-box-direction: normal;
    -webkit-flex-direction: column;
    -ms-flex-direction: column;
    flex-direction: column;
}
```

# 2. Thực hành

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

# 2.1. Vấn đề:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Giả sử ta cột phải ta có rất nhiều element bên dưới nữa, sau khi Vuejs đổ data và render ra view. Và chiều cao của cột phải trên ta ko biết rõ là bao nhiêu (có thể 100px, 200px hay 350px … tùy thuộc vào data được đổ ra)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Tuy nhiên ta muốn cột bên trái --> phải có chiều cao bằng với cột bên phải. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Tới đây có thể vài bạn sẽ nghĩ chúng ta set 1 chiều cao chung cho cả 2 div thế là xong, tuy nhiên vì chiều cao của cột bên phải là ko cố định và nó thay đổi → khi render ra view nó sẽ bị lệch lên lệch xuống 1 chút. Như vậy, chiều cao 2 bên sẽ ko bằng khít với nhau được .Thứ 2 là, khi chiều cao cột bên phải thay đổi, thì chiều cao của cột bên trái cũng không tự thay đổi để bằng cột bên phải được vì chúng ta đã set cứng chiều cao cho cả 2 bên. Do đó chúng ta ko chơi kiểu set cứng height được :D

# 2.2. Ý tưởng:

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

# 3. Tài liệu tham khảo:

* [https://snook.ca/archives/html_and_css/getting_your_di](https://snook.ca/archives/html_and_css/getting_your_di)
* [https://scotch.io/bar-talk/different-tricks-on-how-to-make-bootstrap-columns-all-the-same-height](https://scotch.io/bar-talk/different-tricks-on-how-to-make-bootstrap-columns-all-the-same-height)
