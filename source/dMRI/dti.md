# DTI处理

## FDT

[Diffusion MRI - FMRIB's Diffusion Toolbox (FDT)](https://fsl.fmrib.ox.ac.uk/fsl/docs/#/diffusion/index)

### TOPUP

用的数据没有反向b0，后续补充

### EDDY

```
cd /mnt/e/Neuroimage/TestDataSet/JS_test/ZENGMAO/DTI

fslroi DTI_data DTI_b0 0 1
printf "0 -1 0 0.05" > acqparams.txt

mri_synthstrip -i DTI_b0.nii.gz -o DTI_b0_brain.nii.gz -m DTI_b0_brain_mask.nii.gz
# bet DTI_b0 DTI_b0_brain -m -f 0.3

indx=""
for ((i=1; i<=42; i+=1)); do indx="$indx 1"; done
echo $indx > index.txt

# 这里安装了最新版的FSL（6.0.7），提示最好加上diffusion，并使用.json文件
eddy_cuda10.2 diffusion --imain=DTI_data --mask=DTI_b0_brain_mask --acqp=acqparams.txt --index=index.txt --bvecs=bvecs.txt --bvals=bvals.txt --out=eddy_corrected_data --verbose
```

### DTIFIT

```
dtifit -k eddy_corrected_data -o dti -m DTI_b0_brain_mask -r bvecs.txt -b bvals.txt
```

### BedpostX

```
bedpostx_datacheck ./subject

bedpostx_gpu ./subject
```

### ProbtrackX

```
cd ./subject.bedpostX

probtrackx2_gpu -s merged -m nodif_brain_mask.nii.gz -x ROI_test.nii.gz --opd

# Freesurfer
# mri_convert $SUBJECTS_DIR/sub-JS_test/mri/orig.mgz orig.nii.gz

tkregister2 --mov $SUBJECTS_DIR/sub-JS_test/mri/orig.mgz --targ $SUBJECTS_DIR/sub-JS_test/mri/rawavg.mgz --regheader --reg junk --fslregout freesurfer2struct.mat --noedit

convert_xfm -omat struct2freesurfer.mat -inverse freesurfer2struct.mat

flirt -in dti_FA -ref struct_brain -omat fa2struct.mat
convert_xfm -omat struct2fa.mat -inverse fa2struct.mat

convert_xfm -omat fa2freesurfer.mat -concat struct2freesurfer.mat fa2struct.mat
convert_xfm -omat freesurfer2fa.mat -inverse fa2freesurfer.mat

# 尝试生成volume空间的mask？
mri_concat --combine $SUBJECTS_DIR/sub-JS_test/mri/lh.ribbon.mgz $SUBJECTS_DIR/sub-JS_test/mri/rh.ribbon.mgz --o cortex.nii.gz

flirt -in cortex.nii.gz -ref dti_FA.nii.gz -applyxfm -init freesurfer2fa.mat -out cortex_diff.nii.gz -interp nearestneighbour

```

关于FSL并行的问题：https://neurostars.org/t/fsl-dual-regression-error/27704/3


纤维束提值：

https://community.mrtrix.org/t/how-to-extract-fa-value-from-extracted-fibers/4497

https://community.mrtrix.org/t/fa-value-from-tck-file/2719/4