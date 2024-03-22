BIDS与dcm2bids使用过程记录
===========================

关于BIDS格式：脑影像文件的整理格式。最早接触到BIDS其实是在用ExploreASL的时候，但ExploreASL对BIDS格式的要求并不严格。后来用各种管道式预处理软件（fMRIPrep，QSIPrep，ASLPrep）的时候，才认真地学习了如何整理标准的BIDS格式。

我在学习fMRIPrep的时候，参照了这个教程[fMRIPrep_Tutorial](https://www.bilibili.com/video/BV19P4y1t7Gh?from=search&seid=9005978369037743711&spm_id_from=333.337.0.0)，所以我只试过用[dcm2bids](https://unfmontreal.github.io/Dcm2Bids/3.1.1/)进行BIDS格式的整理（Windows 10）。

## dcm2bids
如前所述，参照的是上面提到视频的后半部分[fMRIPrep_Tutorial](https://www.bilibili.com/video/BV19P4y1t7Gh?from=search&seid=9005978369037743711&spm_id_from=333.337.0.0)。dcm2bids的关键就是创建符合自己数据集的config文件，涉及到不同的模态有相应的规范，还是建议参考[How to create a configuration file](https://unfmontreal.github.io/Dcm2Bids/3.1.1/how-to/create-config-file/)和[Magnetic Resonance Imaging](https://bids-specification.readthedocs.io/en/stable/modality-specific-files/magnetic-resonance-imaging-data.html)。

对上述视频中教程的一些补充：
- 需要修改的`utils.py`文件和视频中的位置不同（应该是dcm2bids版本不同的原因），但我进行的修改相同。
- 官方文档中推荐将config文件放在`code`文件夹下

## BIDS validator
在使用各种Prep时，默认的第一步是检查BIDS格式。如果出现报错的话我喜欢用网页版的[BIDS Validator](https://bids-standard.github.io/bids-validator/)检查BIDS格式是否符合规范，比较快捷方便。