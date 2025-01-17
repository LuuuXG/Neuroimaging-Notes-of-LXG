# Pipelines

---

## Structural MRI (sMRI)

### *freesurfer*

### *freesurfer_clinical*

### *cat12*

---

## CSVD Markers

### *wmh_quantification*

### *pvs_quantification*
```yml
pipelines:
  pvs_quantification:
    use_which_t1w: "acq-highres"
    method: "SHIVA"
    modality: "T1w"
    predictor_files:
      - "/mnt/e/Neuroimage/SHIVA_model/PVS_model/v1/T1.PVS/20211030-162753_Unet3Dv2-10.7.2-1.8-T1.VRS_fold_1x6_pi_fold_0_model.h5"
      - "/mnt/e/Neuroimage/SHIVA_model/PVS_model/v1/T1.PVS/20211030-162753_Unet3Dv2-10.7.2-1.8-T1.VRS_fold_1x6_pi_fold_1_model.h5"
      - "/mnt/e/Neuroimage/SHIVA_model/PVS_model/v1/T1.PVS/20211030-162753_Unet3Dv2-10.7.2-1.8-T1.VRS_fold_1x6_pi_fold_2_model.h5"
      - "/mnt/e/Neuroimage/SHIVA_model/PVS_model/v1/T1.PVS/20211030-162753_Unet3Dv2-10.7.2-1.8-T1.VRS_fold_1x6_pi_fold_3_model.h5"
      - "/mnt/e/Neuroimage/SHIVA_model/PVS_model/v1/T1.PVS/20211030-162753_Unet3Dv2-10.7.2-1.8-T1.VRS_fold_1x6_pi_fold_4_model.h5"
      - "/mnt/e/Neuroimage/SHIVA_model/PVS_model/v1/T1.PVS/20211030-162753_Unet3Dv2-10.7.2-1.8-T1.VRS_fold_1x6_pi_fold_5_model.h5"
    crop_or_pad_percentage: [0.5, 0.5, 0.5]
    save_intermediate_image: false
    threshold: 0.8
```

目前，我们使用[SHIVA models](https://github.com/pboutinaud/SHIVA_PVS)对PVS和CMB进行分割。

### *cmb_quantification*

```yml
pipelines:
  cmb_quantification:
    use_which_swi: ''
    use_which_t1w: "acq-highres"
    method: "SHIVA"
    modality: "swi"
    predictor_files:
      - "/mnt/e/Neuroimage/SHIVA_model/CMB_model/v1/20230328-101347_Unet3Dv2-10.7.2-1.8-SWAN.CMB_fold_CMB_1x3-331_fold_0_model.h5"
      - "/mnt/e/Neuroimage/SHIVA_model/CMB_model/v1/20230329-221948_Unet3Dv2-10.7.2-1.8-SWAN.CMB_fold_CMB_1x3-331_fold_2_model.h5"
      - "/mnt/e/Neuroimage/SHIVA_model/CMB_model/v1/20230330-095525_Unet3Dv2-10.7.2-1.8-SWAN.CMB_fold_CMB_1x3-331_fold_1_model.h5"
    crop_or_pad_percentage: [0.5, 0.5, 0.5]
    save_intermediate_image: true
    threshold: 0.8
```

---

## DWI (dMRI)

### *fdt*

### *qsiprep*

QSIPrep和QSIRecon主要用于处理DSI和multi-shell数据

DSI (and also CS-DSI)数据的预处理参考了：[TOPUP in QSIprep](https://neurostars.org/t/topup-in-qsiprep/20558/6)。关键点在于DSI数据不能使用FSL eddy进行涡流校正。

---

## QSM

### *sepia*

---

## ASL

### *exploreasl*

---

## fMRI