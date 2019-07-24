# bdc-rotorder
Bone Driven Controller with Custom Rotation Order Anim node for UE4

Bone driven controller rotation source around Y is limited to -90°..90° interval, which can be a problem.
For example, if you have an elbow rotating around Y and you have a morph that should be applied between 110° and 135°, the default bone driven controller is useless. 
This node solves the problem by allowing you to specify custom rotation order that will be used for transforming quaternion to euler.
Any order with Y not in the middle will work (YXZ, YZX, ZXY, etc. works; XYZ,ZYX does not work)
In general, the middle axis in the order will be limited to -90°..90°, two others will be -180°..180.
If you have a bone rotating a lot around 2 axis, put them on the sides of the order, like for the shoulder rotating around Y and Z but not X use ZXY or YXZ

The function QuatToEuer comes from the new Control Rig plugin.

The code is for UE 4.22. 

The modified parts are:

- in the AnimNode_BoneDrivenController.h the RotationOrder enum; then RotationOrder property and EulerFromQuat method.
- in the AnimNode_BoneDrivenController.cpp EulerFromQuat implementation and modified ExtractSourceValue method where it's used



	else if (SourceComponent < EComponentType::Scale)
	{
    //new code
		const FVector RotationDiff = EulerFromQuat(InCurrentBoneTransform.GetRotation() * InRefPoseBoneTransform.GetRotation().Inverse(), RotationOrder/*ERotationOrder::XZY*/);
		SourceValue = (SourceComponent == EComponentType::RotationZ ? 1.0f : -1.0f)*RotationDiff[(int32)(SourceComponent - EComponentType::RotationX)];

    //original code
		//const FVector RotationDiff = (InCurrentBoneTransform.GetRotation() * InRefPoseBoneTransform.GetRotation().Inverse()).Euler();
		//SourceValue = RotationDiff[(int32)(SourceComponent - EComponentType::RotationX)];

	}
  
  
  I had to add to multiply to -1 because EulerFromQuat will not give the same sigh as FQuat::Euler() method.
