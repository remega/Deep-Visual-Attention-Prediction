This repo contains a CAFFE re-implementation for 

# "Deep Visual Attention Prediction", TIP18

By Wenguan Wang, Jianbing Shen

========================================================================

Our current implementation is based on the caffe version of Holistically-Nested Edge Detection (has been included in this repository, see `external/caffe`). We also use some functions from fasterrcnn's caffe.
But it can be easily implemented in standard caffe library. 


### Re 
As some apis of faster-rcnn-caffe are used, we should compile the provided caffe. That caffe is supposed to use cudnn4.0, so don't use cudnn when compiling, if your cudnn is more current. I also add USE_OPENCV := 0 in makeconfig

use new caffe-fast-rcnn  https://github.com/rbgirshick/caffe-fast-rcnn/tree/0dcd397b29507b8314e252e850518c5695efbb83

cd caffe-fast-rcnn  
git init
git remote add caffe https://github.com/BVLC/caffe.git  
git fetch caffe  
git merge -X theirs caffe/master  

Remove self_.attr("phase") = static_cast<int>(this->phase_); from include/caffe/layers/python_layer.hpp after merging.
    
As some apis of faster-rcnn-caffe are used, we should compile the provided caffe. That caffe is supposed to use cudnn4.0,so 
1. Use a modified fasrer-rcnn-caffe 
https://github.com/soulslicer/caffe-fast-rcnn
2. modify caffe/matlab/+caffe/Net.m by adding this function: (just copy following codes into the end of Net.m)
function reshape_as_input(self, input_data)
CHECK(iscell(input_data), 'input_data must be a cell array');
CHECK(length(input_data) == length(self.inputs), ...
'input data cell length must match input blob number');
% reshape input blobs
for n = 1:length(self.inputs)
if isempty(input_data{n})
continue;
end
input_data_size = size(input_data{n});
input_data_size_extended = [input_data_size, ones(1, 4 - length(input_data_size))];
self.blobs(self.inputs{n}).reshape(input_data_size_extended);
end
self.reshape();
end


只能用自带caffe 因为cropping层被修改过，上述方法跑通结果依旧有bug（位置偏移）。 自带fasterrcnn-caffe版本太老，cudnn5不兼容，且必须编译opencv3以上。
另注意在matlab运行caffe时确保环境变量正确（make mattest可能会报错也是这个原因）

We plan to re-implement it on Keras, but it depends on my schedule.

a. Please first compile the 'Caffe' for matlab.

b. Please download our model and results on DUT, MIT300, MIT1003, Pascal, and Toronto datasets from 

google drive: https://drive.google.com/open?id=1cl3k3POMqS5obv0ffz84qOgGfAbEmtB3

or Baidu Wangpan: https://pan.baidu.com/s/1o8kQcAY password: wdwa

and put the model 'attention_final' into 'models' folder.

c. Run 'deep_attention_test' and the results can be found in 'datasets/results/'.

========================================================================

The saliency results on MIT300, MIT1003, TORONTO, PASCAL-S and DUT datasets can be found at

https://drive.google.com/file/d/1ana0Qv4nDvynDyXG3rCfOt4gEQ4CdiPP/view?usp=sharing

========================================================================

If you find our method useful in your research, please consider citing:

    @article{wang2018deep,
        Author = {Wenguan Wang, Jianbing Shen},
        Title = {Deep Visual Attention Prediction},
        Journal = {IEEE Transactions on Image Processing},
        Year = {2018}
    }

========================================================================

Any comments, please email: wenguanwang.china@gmail.com
