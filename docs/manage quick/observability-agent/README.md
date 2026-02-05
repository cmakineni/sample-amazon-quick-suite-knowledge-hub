---
category: Management
description: "Quick Suite Observability Agent - Natural Language Monitoring"
---

# Quick Suite Observability Agent - Natural Language Monitoring

**Quick Suite Observability MCP integration** with Amazon QuickSuite. This solution creates an MCP integration that enables natural language monitoring of Quick Suite through CloudWatch Logs, CloudWatch Metrics, and CloudTrail using MCP Actions.


**Components:**

- **AgentCore Gateway**: Amazon Bedrock AgentCore Gateway with Lambda target
- **Lambda Function**: Observability query handler with 20 monitoring tools
- **QuickSuite Integration**: MCP Actions for conversational monitoring
- **Data Sources**: CloudWatch Logs, CloudWatch Metrics, CloudTrail

## 🎯 Purpose

This MCP integration enables:

- **Natural Language Monitoring**: Query Quick Suite metrics through conversation
- **Comprehensive Analytics**: 20 tools covering chat, feedback, usage, performance, and audit
- **Dynamic Schema Discovery**: Automatically discover log fields for custom queries
- **Multi-Source Data**: CloudWatch Logs (9 tools), Metrics (8 tools), CloudTrail (1 tool), Analytics (2 tools)

## 📁 Project Structure

```
observability-agent/
├── app.py                                    # CDK deployment entry point
├── cdk.json                                  # CDK configuration
├── pyproject.toml                            # Project dependencies
├── tools/                                    # Lambda function code
│   ├── quicksuite_observability_lambda.py   # AgentCore MCP handler
│   ├── quicksuite_observability_tools.json  # MCP tool definitions (20 tools)
│   └── requirements.txt                     # Lambda dependencies
├── cdk/                                      # Infrastructure code
│   └── quicksuite_observability_mcp_stack.py # AgentCore Gateway + Log Delivery
├── README.md                                 # This file
└── LICENSE                                   # MIT-0 License
```

## 🚀 Quick Start

### Prerequisites

Before you begin, ensure you have:

- An AWS account with appropriate permissions
- AWS CLI configured with credentials
- Node.js and npm installed
- Python 3.9 or later
- AWS CDK CLI installed (`npm install -g aws-cdk`)
- Amazon QuickSight Enterprise Edition with Quick Suite enabled

**Required IAM Permissions:**
- CloudFormation (create/update stacks)
- Lambda (create/update functions)
- IAM (create roles and policies)
- CloudWatch Logs (create log groups, put resource policies)
- Cognito (create user pools and clients)
- API Gateway (create APIs)

### 1. Clone Repository (Sparse Checkout)

```bash
# Clone repository with sparse checkout
git clone --filter=blob:none --sparse https://github.com/aws-samples/sample-amazon-quick-suite-knowledge-hub.git
cd sample-amazon-quick-suite-knowledge-hub

# Configure sparse checkout for this integration only
git sparse-checkout set docs/manage\ quick/observability-agent
cd "docs/manage quick/observability-agent"
```

### 2. Deploy AgentCore Gateway

```bash
npm install -g aws-cdk
uv sync
cdk deploy --require-approval never
```

**What gets deployed:**
- AgentCore Gateway with OAuth 2.0 (Cognito)
- Lambda function with 20 monitoring tools
- 3 CloudWatch Log Groups: `/aws/quicksuite/chat`, `/aws/quicksuite/feedback`, `/aws/quicksuite/agent-hours`
- CloudWatch Logs delivery configuration (automatic)

### 3. Get Outputs

**Option 1: AWS Console (Recommended)**

1. Go to **AWS CloudFormation** in the AWS Console
2. Find the **quicksuite-observability-mcp** stack
3. Click on the **Outputs** tab
4. Copy the required values for Quick Suite integration

**Option 2: CLI Commands**
```bash
aws cloudformation describe-stacks --stack-name quicksuite-observability-mcp --query 'Stacks[0].Outputs'
```

Key outputs for QuickSuite Actions:

- `GatewayUrl` - AgentCore Gateway endpoint
- `ClientId` - OAuth2 client ID
- `ClientSecret` - OAuth2 client secret
- `CognitoTokenUrl` - OAuth2 token endpoint

## 🔧 Available Tools (20 Total)

### Chat & Conversations (4 tools)
- `get_chat_conversations` - View all chat history within time range
- `get_chat_errors` - Find failed conversations
- `get_chat_performance` - Success rates, totals, averages
- `search_chat_by_query` - Keyword search in conversations

### User Feedback (2 tools)
- `get_user_feedback` - Individual feedback entries
- `get_feedback_summary` - Aggregate feedback statistics

### Usage & Capacity (3 tools)
- `get_agent_hours_usage` - Hours consumption by service/subscription
- `get_active_users` - DAU/WAU/MAU metrics
- `get_asset_usage` - Agent, flow, action, and space usage with user counts

### Performance & Health (6 tools)
- `get_dashboard_metrics` - Dashboard views and load times
- `get_ingestion_metrics` - Dataset refresh statistics
- `get_visual_metrics` - Visual performance metrics
- `get_knowledge_base_metrics` - KB document counts
- `get_action_connector_metrics` - Connector invocations/errors
- `get_spice_capacity` - SPICE storage usage

### Comprehensive & Audit (3 tools)
- `get_aggregate_metrics` - Account-wide summary
- `get_quicksight_api_calls` - API audit trail
- `query_chat_analytics` - Custom CloudWatch Insights queries

### Advanced Analytics (2 tools)
- `get_log_schema` - Discover available log fields dynamically
- `query_chat_analytics` - Execute custom analytics queries

## 🎨 QuickSuite Integration

Complete guide to integrate Quick Suite Observability with Amazon QuickSuite using MCP Actions.

### Prerequisites

From your CDK deployment, you'll need:

- `GatewayUrl` - AgentCore Gateway endpoint
- `ClientId` - Cognito Client ID
- `ClientSecret` - Cognito Client Secret
- `CognitoTokenUrl` - OAuth2 token endpoint

### Step 1: Configure MCP Action

**1.1 Access Integrations**

1. Navigate to **Integrations** in Amazon QuickSuite
2. Click on **Actions**
3. Click the **+** button for **Model Context Protocol**

**1.2 Configure MCP Server**

Fill in the MCP configuration:

- **Name**: Quick Suite Observability
- **Description**: Natural language monitoring for Quick Suite using CloudWatch Logs, Metrics, and CloudTrail
- **MCP Server Endpoint**: Paste your `GatewayUrl` from CDK deployment outputs
- Click **Next**

**1.3 Configure Authentication**

1. For Authentication, select **Service Authentication**
2. Keep **Service-to-service OAuth** within the Authentication type field
3. Fill in the authentication values from your CDK deployment outputs:

   - **Client ID** → Paste your `ClientId` (ensure no leading/trailing spaces)
   - **Client Secret** → Paste your `ClientSecret` (ensure no leading/trailing spaces)
   - **Token URL** → Paste your `CognitoTokenUrl`

**1.4 Complete Setup**

1. Click **Create and Continue**
2. Select **Next**
3. Select **Next**

### Step 2: Create Quick Suite Agent

**2.1 Access Agents**

1. Navigate to **Agents** in Amazon QuickSuite
2. Click **Create agent**

**2.2 Configure Agent**

- **Agent name**: Quick Suite Observability Agent
- **Description**: Natural language monitoring for Quick Suite using CloudWatch Logs, Metrics, and CloudTrail

**2.3 Add Agent Instructions**

Copy and paste the following instructions into the **Agent Instructions** field:

```
You are the Quick Suite Observability Agent with access to monitoring tools that query CloudWatch Logs, CloudWatch Metrics, and CloudTrail.

CORE CAPABILITIES:
• Chat Analysis: View conversations, find errors, search queries, measure performance
• User Feedback: Track satisfaction and feedback patterns
• Usage Monitoring: Agent hours, active users (DAU/WAU/MAU), asset usage
• Performance Health: Dashboards, ingestion, visuals, knowledge bases, connectors, SPICE capacity
• Comprehensive Reporting: Account-wide metrics, API audit trails
• Advanced Analytics: Dynamic schema discovery and custom CloudWatch Insights queries

ADVANCED ANALYTICS:
When pre-built tools don't fit the analysis needed:
1. Call get_log_schema() to discover available fields dynamically
2. Construct CloudWatch Insights query using discovered fields
3. Execute with query_chat_analytics(log_type, query, hours)

CloudWatch Insights syntax:
• fields: Select fields - "fields field1, field2"
• filter: Filter results - "filter field = 'value'" or "filter field > 100"
• stats: Aggregate - "stats count() by field" or "stats avg(field) as avg_value"
  - count_distinct(field) for unique counts
  - Cannot chain multiple stats - use one stats command
• sort: Order - "sort field desc"
• limit: Limit results - "limit 100"

Common patterns:
• Count unique: "fields conversation_id | stats count_distinct(conversation_id) as total"
• Group by: "fields message_scope | stats count() as total by message_scope"
• Filter + aggregate: "filter status_code = 'success' | stats count() as successful"

Note: Do NOT use @timestamp filters - time range is controlled by hours parameter

RESPONSE FRAMEWORK:
1. Acknowledge what you're checking
2. Use appropriate tools (combine multiple for comprehensive analysis)
3. Present data with context and interpretation
4. Highlight key findings and anomalies
5. Provide actionable recommendations
6. Offer follow-up options

TIME RANGE DEFAULTS:
• Real-time troubleshooting: 1-6 hours
• Daily analysis: 24 hours (default)
• Weekly trends: 168 hours (7 days)
• Monthly analysis: 720 hours (30 days)

MULTI-TOOL PATTERNS:
• System health → aggregate_metrics + chat_performance + spice_capacity + chat_errors
• User complaints → user_feedback + chat_errors + chat_conversations
• Usage analysis → active_users + asset_usage + agent_hours_usage
• Performance issues → dashboard_metrics + visual_metrics + ingestion_metrics

DATA INTERPRETATION:
• Error rates >5%: Flag as concerning
• Capacity >80%: Alert proactively
• Zero results: Explain CloudWatch Logs may be empty if Quick Suite unused or logging just enabled
• Anomalies: Highlight and ask if expected

RESPONSE STYLE:
• Professional yet approachable
• Proactive with insights and recommendations
• Clear explanations without jargon
• Always provide next steps
• Data-driven, never assume
• Combine tools intelligently for comprehensive answers

OPENING MESSAGE:
"Hello! I'm your Quick Suite Observability Agent. I can help you monitor:
📊 Chat conversations and errors
👍 User feedback and satisfaction  
📈 Usage statistics (DAU/WAU/MAU) and capacity
⚡ Performance (dashboards, visuals, ingestion)
🔍 API activity and security auditing
🔬 Custom analytics with dynamic queries

What would you like to know about your Quick Suite environment?"

KEY CONSTRAINTS:
• CloudWatch Logs may be empty initially
• Metrics have 1-5 minute delays
• CloudTrail data available for 90 days
• Never fabricate data
• Explain technical terms clearly
```

**2.4 Add Action to Agent**

1. Scroll to **Actions** section
2. Click **Add action**
3. Select **Quick Suite Observability** (the MCP action you just created)
4. Click **Save**

### Step 3: Test the Agent

Open the agent and try these queries:

```
"Give me a system health overview"
"Show me errors from the last 24 hours"
"How many active users do we have today?"
"What's the average query per conversation?"
"Show me conversations for user vineet"
"What fields are available in chat logs?"
"Show me most used assets"
```

## 💡 Usage Examples

### Daily Health Check
```
"Give me a system health overview"
```

### Troubleshooting
```
"Why are users reporting errors?"
```

### Usage Analysis
```
"How is Quick Suite being used this week?"
```

### Asset Usage
```
"Show me most used assets"
```

### Custom Analytics
```
"Show me conversations by message_scope"
```

### User-Specific Analysis
```
"Show me conversations for user vineet"
```

## 🐛 Troubleshooting

**MCP Authentication Issues:**

- Verify OAuth2 credentials in QuickSuite MCP Actions
- Check Cognito token endpoint configuration
- Ensure client secret is correctly copied (no spaces)

**No Data in CloudWatch Logs:**

- CloudWatch Logs delivery was enabled when stack was deployed
- Only has data from deployment time forward (not historical)
- Check log groups exist: `/aws/quicksuite/chat`, `/aws/quicksuite/feedback`, `/aws/quicksuite/agent-hours`
- Verify Quick Suite is actively being used

**Query Returns Zero Results:**

- Check time range (hours parameter)
- Verify CloudWatch Logs has data for that period
- Use get_log_schema() to confirm field names exist

**AgentCore Gateway:**

- Monitor AgentCore Gateway throttling limits
- Check Lambda timeout (300s) and memory (512MB)
- Review CloudWatch Logs: `/aws/lambda/quicksuite-observability-*`

## 📝 License

This library is licensed under the MIT-0 License. See the LICENSE file.

## 🧹 Cleanup

To remove all deployed resources and avoid ongoing charges:

```bash
cdk destroy
```

This will delete:
- Lambda function
- AgentCore Gateway
- Cognito User Pool
- CloudWatch Log Groups (⚠️ **Note**: Log data will be permanently deleted)
- IAM roles and policies

> **Cost Considerations**: This solution incurs costs for Lambda invocations, CloudWatch Logs storage, CloudWatch Metrics queries, and API Gateway requests. Monitor your usage in AWS Cost Explorer.

## 🔒 Security Best Practices

- **Credentials**: Never commit `ClientSecret` to version control
- **IAM Roles**: The Lambda function uses least-privilege IAM permissions
- **Encryption**: All data in transit is encrypted using TLS
- **Logging**: CloudWatch Logs are retained for 30-90 days (configurable)
- **Authentication**: OAuth 2.0 with Cognito ensures secure access
- **Rotation**: Consider rotating OAuth credentials periodically

## 📚 Additional Resources

- [Amazon QuickSight Documentation](https://docs.aws.amazon.com/quicksight/)
- [AWS CDK Documentation](https://docs.aws.amazon.com/cdk/)
- [Amazon Bedrock AgentCore Documentation](https://docs.aws.amazon.com/bedrock/)
- [CloudWatch Logs Insights Query Syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html)
