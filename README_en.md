# Bedrock Prompt Management System

A Python utility system for efficiently managing prompts in Amazon Bedrock Prompt Management. Provides environment-specific prompt management, version control, and rollback functionality using AWS Systems Manager Parameter Store.

## 📁 Project Structure

```
bedrock_prompt_management_system/
├── README.md
├── bedrock_prompt_management_version_control_simple.py    # Basic prompt retrieval utility
└── bedrock_prompt_management_version_control_advanced.py  # Advanced version control and rollback system
```

## 🚀 Key Features

### 📝 Simple Version (Basic Prompt Retrieval Utility)
- **Prompt Text Retrieval**: Simple prompt content retrieval through Parameter Store
- **Environment Status Check**: Compare prompt status between DEV/PROD environments
- **Prompt Comparison**: Check content consistency between two environments

### 🏷️ Advanced Version (Advanced Version Control)
- **Tag-based Version Management**: Create versions with meaningful tags (composite tags)
- **Cross-environment Promotion**: Automated DEV → PROD promotion process
- **Rollback Functionality**: Safe rollback to previous versions
- **Interactive Interface**: User-friendly CLI interface

## 🎯 Usage Scenarios

**When you only need simple retrieval**
→ Use `bedrock_prompt_management_version_control_simple.py`

**When version management is required**
→ Use `bedrock_prompt_management_version_control_advanced.py`

**When production deployment is needed**
→ Use the promote feature in `bedrock_prompt_management_version_control_advanced.py`

## 🛠️ Quick Start

### 1. Install Packages
```bash
pip install boto3 botocore
```

### 2. Configure AWS Credentials
```bash
aws configure
```

### 3. Configure Parameter Store (Required)
```
aws ssm put-parameter --name "/prompts/{your-application-name}/dev/current" --value "YOUR_PROMPT_ID"
aws ssm put-parameter --name "/prompts/{your-application-name}/prod/current" --value "YOUR_PROMPT_ID"
```
💡 **Note**: Replace `{your-application-name}` with your actual application name (e.g., text2sql, chatbot, summarizer, etc.)

### 4. Simple Prompt Retrieval
```bash
python3 bedrock_prompt_management_version_control_simple.py
```

### 5. Interactive Version Management
```bash
python3 bedrock_prompt_management_version_control_advanced.py
```

## 📋 Example of execution results

### Simple Version execution results
============================================================
📝 1. Retrieving prompts from all environments:
------------------------------------------------------------

🌍 Environment: DEV (Development Environment)
📍 Parameter Path: /prompts/text2sql/dev/current
📝 Simple text retrieval:
------------------------------------------------------------
Retrieved Prompt identifier from Parameter Store: 1MVVP7OY39
✅ Prompt text: Tell me kimchi recipe in Korean.
----------------------------------------

🌍 Environment: PROD (Production Environment)
📍 Parameter Path: /prompts/text2sql/prod/current
📝 Simple text retrieval:
------------------------------------------------------------
Retrieved Prompt identifier from Parameter Store: ZWAL5F16XF
✅ Prompt text: Tell me kimbab recipe in Korean.
----------------------------------------

============================================================
🌍 2. Environment-based Prompt Status:
------------------------------------------------------------
Retrieved Prompt identifier from Parameter Store: 1MVVP7OY39
✅ DEV      | dev | Tell me kimchi recipe in Korean....
Retrieved Prompt identifier from Parameter Store: ZWAL5F16XF
✅ PROD     | prod | Tell me kimbab recipe in Korean....

============================================================
📊 3. Prompt Comparing DEV vs PROD:
------------------------------------------------------------
Retrieved Prompt identifier from Parameter Store: 1MVVP7OY39
Retrieved Prompt identifier from Parameter Store: ZWAL5F16XF


Parameter 1: /prompts/text2sql/dev/current
Content: Tell me kimchi recipe in Korean.

Parameter 2: /prompts/text2sql/prod/current
Content: Tell me kimbab recipe in Korean.

🔍 Same content: ❌ No

============================================================

### Advanced Version execution results

🚀 Starting Prompt Version Control
This demo will show you how to:
  • Select working environment (DEV/PROD)
  • Create tagged versions
  • List versions with tags
  • Rollback to previous versions
  • Promote between environments
🌍 Environment Selection
========================================
Available environments:
  DEV: Development Environment
    Parameter Store: /prompts/text2sql/dev/current
  PROD: Production Environment
    Parameter Store: /prompts/text2sql/prod/current

👉 Select environment (dev/prod): dev
🎯 Initialized for Development Environment
📍 Parameter Store: /prompts/text2sql/dev/current
✅ Retrieved Prompt ID from DEV: 1MVVP7OY39

🎯 Using Prompt ID: 1MVVP7OY39
🌍 Working in DEV environment

============================================================
🏷️  Bedrock Prompt Version Control & Rollback Demo (DEV)
============================================================
1. 📋 List all versions with tags
2. 🏷️  Create new tagged version
3. 🔄 Rollback to specific version
4. 🚀 Promote version between environments
5. 🔄 Switch environment
6. 🚪 Exit

👉 Select option (1-6): 2

🏷️ Creating new tagged version in DEV...
Enter new content: Tell me my mom's kimchi recipe. 
Enter version tag (default: v1.0.0-dev): v1.5-dev
Enter description (optional): 
✅ Created version 16 with tags:
   Environment: DEV
   Status: TESTING
   Version: v1.5-dev
   CreatedDate: 2025-07-09
   CreatedTime: 17:27:25
   SourceEnvironment: DEV
✅ Created version 16 successfully!

============================================================
🏷️  Bedrock Prompt Version Control & Rollback Demo (DEV)
============================================================
1. 📋 List all versions with tags
2. 🏷️  Create new tagged version
3. 🔄 Rollback to specific version
4. 🚀 Promote version between environments
5. 🔄 Switch environment
6. 🚪 Exit



## 🏷️ Tag System

### Auto-generated Tags
- **Version**: Version tag (e.g., v1.2.0)
- **CreatedDate**: Creation date
- **CreatedTime**: Creation time
- **Environment**: Environment information
- **SourceEnvironment**: Source environment

### Additional Tags for Promotion
- **PromotedFrom**: Source environment for promotion
- **PromotedDate**: Promotion date
- **SourcePromptId**: Source Prompt ID
- **PromotionType**: Type of promotion

### Additional Tags for Rollback
- **RollbackFrom**: Source version for rollback
- **RollbackTo**: Target version for rollback
- **RollbackReason**: Reason for rollback
- **Status**: ROLLBACK_COMPLETE

## ⚠️ Important Notes

1. **Permission Setup**: The following AWS permissions are required:
   - `ssm:GetParameter` (Parameter Store read)
   - `bedrock:GetPrompt` (Prompt retrieval)
   - `bedrock:UpdatePrompt` (Prompt modification)
   - `bedrock:CreatePromptVersion` (Version creation)
   - `bedrock:TagResource` (Tag management)

2. **Parameter Store Setup**: Prompt IDs must be configured in Parameter Store before use.

3. **Region Configuration**: Default is `us-west-2`, can be changed as needed.

4. **Version Limitations**: Currently, Bedrock Prompt can only create up to 10 versions maximum. If you attempt to create a new version beyond this limit, a ValidationException error will occur. When the 10-version limit is reached, you must delete older versions that are no longer in use. Even after deleting versions, version numbers are not reused. This is by design to maintain consistency in version tracking. For example, if versions 1-10 currently exist and you delete version 1 and then create a new version, version 11 will be created.

---

**Last Updated**: 2025-07-09
**Version**: 1.0.0
