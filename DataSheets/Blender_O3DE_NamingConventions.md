# Cross-platform naming conventions with O3DE-priority

Main focus on Blender and O3DE, while maintaining some coherence between other CADs, Animation and Game Engines. This is for reference only and subject to change.

- avoid periods . Use `_L` and `_R` for left/right; use `_01_105` for versions
- avoid spaces and special characters

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
| Character root | CHR_<Name> |
| Skeletal mesh / skinned geometry | SK_<Name> |
| Static mesh / geometry | SM_<Name> |
| Rig / armature object | RIG_<Name> |
| Bone | upperArm_L, fingerIndex_02_R |
| Controller / helper | CTRL_<Region> |
| Collision mesh | COL_<Asset>_<Type> |
| Physics asset | PHYS_<Asset> |
| LOD variant | <Name>_LOD0–LOD3 |

---

## Animations

| Asset type | Recommended name |
|---|---|
| Animation clip / action | AN_<Character>_<Action> |
| Additive layer | AN_<Action>_Add |
| Loop / one-shot tag | AN_<Action>_Loop, AN_<Action>_1Shot |
| State name (anim graph) | ST_<Action> |
| Retarget set | RET_<Source>_to_<Target> |
| Versioning | <Name>_v01_103_05 |

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

