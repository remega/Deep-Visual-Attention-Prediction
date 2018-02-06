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
另注意在matlab运行caffe时确保环境变量正确（make mattest可能会报错也是这个原因） 路径为编译caffe时各个lib的路径，包括anaconda，anaconda/env (有libopencv)，cuda等
