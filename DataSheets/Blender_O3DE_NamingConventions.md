# Cross-platform naming conventions with O3DE-priority

Main focus on Blender and O3DE, while maintaining some coherence between other CADs, Animation and Game Engines. This is for reference only and subject to change.

- avoid periods . Use `_L` and `_R` for left/right; use `_01_105` for versions
- avoid spaces and special characters
- this is a work-in-progress

---

## Variables and constants

| Item | Convention | Examples | Notes |
|---|---|---|---|
| Local variables & parameters | lowerCamelCase | maxSpeed, healthPoints, deltaTime | Default for all non-constant variables |
| Member variables (class/struct) | m_ + lowerCamelCase | m_currentHealth, m_isActive | Storage class prefix is required |
| Static member variables | s_ + lowerCamelCase | s_instanceCount, s_cachedData | Storage class prefix is required |
| Global variables | g_ + lowerCamelCase | g_frameNumber, g_config | Discouraged and forbidden for non-POD types |
| Constants | PascalCase | MaxSpeed, DefaultHealth | Keep consistent |
| No Hungarian notation | Do not use type prefixes | — | Avoid iCount, fTime, pBuffer, etc. |

---

## Characters, rigs, meshes

| Asset type | Recommended name |
|---|---|
| Character root | CHR_ |
| Skeletal mesh / skinned geometry | SK_ |
| Static mesh / geometry | SM_ |
| Rig / armature object | RIG_ |
| Bone | upperArm_L, fingerIndex_02_R |
| Controller / helper | CTRL_<Region> |
| Collision mesh | COL_<Asset>_<Type> |
| Physics asset | PHYS_<Asset> |
| LOD variant | _LOD0_LOD3 |

---

## Humanoid bones

| Group | Recommended name(s) | Notes |
|---|---|---|
| Root / hips | root; pelvis | `root` or `pelvis` as scene root joint |
| Pelvis hips | hip_L; hip_R | use if separate from `pelvis` |
| Thigh / upper leg | thigh_01_L; thigh_01_R | or `upperLeg_01_L` if preferred |
| Knee / lower thigh | knee_01_L; knee_01_R | keeps kinematic clarity |
| Shin / lower leg | shin_01_L; shin_01_R | or `lowerLeg_01_L` |
| Ankle | ankle_L; ankle_R | |
| Ball / foot | ball_L; ball_R | foot pivot bone |
| Toe / toe tip | toe_01_L; toe_01_R | numbered if multiple toes: toe1_01_L etc. |
| Spine chain | spine_01; spine_02; spine_03; spine_04 | increment outward from pelvis to chest |
| Chest / thorax | chest | common single bone after spine chain |
| Upper chest / clavicle base | clavicle_L; clavicle_R | clavicle before shoulder |
| Shoulder / scapula | shoulder_01_L; shoulder_01_R | optional if separate from clavicle |
| Upper arm | upperArm_01_L; upperArm_01_R | or `arm_01_L` |
| Elbow / forearm | elbow_01_L; elbow_01_R | or `lowerArm_01_L` |
| Lower arm / forearm | lowerArm_01_L; lowerArm_01_R | |
| Wrist | wrist_L; wrist_R | |
| Hand / palm | hand_L; hand_R | parent of finger roots |
| Thumb (digit 1) | thumb_01_L; thumb_02_L; thumb_03_L; thumb_01_R; thumb_02_R; thumb_03_R | three phalanges: _01 (proximal) → _03 (tip) |
| Index (digit 2) | fingerIndex_01_L; fingerIndex_02_L; fingerIndex_03_L; fingerIndex_01_R; fingerIndex_02_R; fingerIndex_03_R | use `fingerIndex`, `fingerMiddle`, `fingerRing`, `fingerPinky` for clarity |
| Middle (digit 3) | fingerMiddle_01_L; fingerMiddle_02_L; fingerMiddle_03_L; fingerMiddle_01_R; fingerMiddle_02_R; fingerMiddle_03_R | |
| Ring (digit 4) | fingerRing_01_L; fingerRing_02_L; fingerRing_03_L; fingerRing_01_R; fingerRing_02_R; fingerRing_03_R | |
| Little / pinky (digit 5) | fingerPinky_01_L; fingerPinky_02_L; fingerPinky_03_L; fingerPinky_01_R; fingerPinky_02_R; fingerPinky_03_R | |
| Thumb metacarpal (optional) | thumb_metacarpal_L; thumb_metacarpal_R | if rig uses explicit metacarpal bones |
| Neck chain | neck_01; neck_02 | ascend from chest to head |
| Head | head | top of neck chain |
| Jaw | jaw | lower jaw / mandible |
| Eyes | eye_L; eye_R | common eye pivot bones |
| Eyeballs / look target | eyeTarget_L; eyeTarget_R | optional IK/look targets |
| Eyebrows (bones) | browInner_L; browOuter_L; browInner_R; browOuter_R | optional facial rig bones |
| Cheeks / facial | cheek_L; cheek_R | optional |
| Tongue (root to tip) | tongue_01; tongue_02; tongue_03 | optional internal chain |
| Tail (if present) | tail_01; tail_02; ... tail_N | N increments away from pelvis |
| Prop attachment points | attach_Weapon_R; attach_Shield_L; attach_Hat | generic attachment sockets |
| Root control (controller) | CTRL_root | controller naming convention: prefix `CTRL_` |
| FK controller examples | CTRL_upperArm_L; CTRL_hand_L | controller helper names follow `CTRL_` prefix |
| IK controller examples | CTRL_hand_IK_L; CTRL_foot_IK_R | use `_IK` / `_FK` suffixes for layer clarity |
| Pole vectors / knee targets | PV_knee_L; PV_knee_R | `PV_` prefix common for pole vector targets |
| Helpers / offsets | offset_pelvis; offset_spine_01 | use `offset_` prefix for temporary transform nodes |

Notes:
- Chain indices should increase from root outward (e.g., spine_01 near pelvis → spine_04 near chest).  
- Finger naming uses semantic names (thumb, fingerIndex, fingerMiddle, fingerRing, fingerPinky) to aid retargeters.  
- Controller nodes should be clearly prefixed (CTRL_, PV_, offset_) and kept separate from actual joint names.  
- Keep names unique within the skeleton; avoid duplicate bone names with different cases.  
- For non-human rigs (quadrupeds, creatures), follow the same patterns: descriptive region + index + side.

---

## Animations

| Asset type | Recommended name |
|---|---|
| Animation clip / action | AN_<Character>_<Action> |
| Additive layer | AN_<Action>_Add |
| Loop / one-shot tag | AN_<Action>_Loop, AN_<Action>_1Shot |
| State name (anim graph) | ST_<Action> |
| Retarget set | RET_<Source>_to_<Target> |
| Versioning | _v01_103_05 |

---

## Materials, textures, shaders

| Asset type | Recommended name |
|---|---|
| Material (base) | M_<Domain>_<Desc> |
| Material instance | MI_<Base>_<Variant> |
| Texture / image | T_<Domain>_<Desc>_<MapType> |
| Shader / graph | SH_<Pipeline>_<Domain>_<Desc> |

---

## Quick examples

| Purpose | Example |
|---|---|
| Character root | CHR_Alex |
| Rig | RIG_Alex |
| Body skeletal mesh | SK_Alex_Body |
| Hand bone (O3DE-safe) | hand_R |
| Walk loop animation | AN_Alex_Walk_Loop |
| Base skin material | M_Skin_Base |
| Tanned material instance | MI_Skin_Tanned |
| BaseColor texture | T_Skin_Base_BaseColor |
| Normal texture | T_Skin_Base_Normal |
| Mirror modifier | MOD_Mirror |
| Armature bind modifier | MOD_Armature_RIG_Alex |
| Collision capsule | COL_Alex_Capsule |
| Prefab / entity | prefab_Alex |

---

