{
  "name": "CrisisSignal AI – Intelligent Disaster Response Hub by shehara and kathir",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "hours"
            }
          ]
        }
      },
      "id": "40fb885c-9077-447c-a236-2262ff465d29",
      "name": "Run Daily at 8 AM",
      "type": "n8n-nodes-base.scheduleTrigger",
      "position": [
        -592,
        560
      ],
      "typeVersion": 1.1
    },
    {
      "parameters": {
        "jsCode": "// Get the raw output string from the previous node.\nconst rawOutput = $input.first().json.output;\n\n// The AI often wraps JSON in Markdown code blocks (```json ... ```).\n// We need to extract the pure JSON string from inside the fences.\n// This regex will find the content between the fences. If no fences are found,\n// it will fall back to using the entire rawOutput string.\nconst jsonStringMatch = rawOutput.match(/```json\\s*([\\s\\S]*?)\\s*```/);\nconst jsonString = jsonStringMatch ? jsonStringMatch[1] : rawOutput.trim();\n\n// Now, try to parse the *cleaned* string.\ntry {\n  const alerts = JSON.parse(jsonString);\n  \n  // Check if the result is a non-empty array.\n  if (Array.isArray(alerts) && alerts.length > 0) {\n    // Success! Pass the alerts to the next node.\n    return [{ json: { alerts } }];\n  }\n} catch (e) {\n  // This will catch errors if the cleaned string is still not valid JSON.\n  console.error(\"NixGuard did not return a valid JSON array even after cleaning:\", e);\n  console.error(\"String that failed to parse:\", jsonString);\n}\n\n// If parsing fails, it's not an array, or it's empty, return no items.\n// This will correctly route the workflow to the 'false' branch of the IF node.\nreturn [];"
      },
      "id": "e5f01889-2ae6-4563-8f34-836f1556e1f0",
      "name": "Parse Alert Array",
      "type": "n8n-nodes-base.code",
      "position": [
        224,
        560
      ],
      "typeVersion": 2
    },
    {
      "parameters": {
        "values": {
          "string": [
            {
              "name": "chatInput",
              "value": "=Act as a senior security analyst reporting to a non-technical executive. Analyze the following JSON array of all high-severity security alerts from the last 24 hours.\n\nYour response MUST be a single, valid JSON object and nothing else. Do not include any text before or after the JSON, and do not wrap it in Markdown code fences (```json).\n\nYour output must strictly adhere to the following structure:\n{\n  \"ai_priority\": \"<'Critical' | 'High' | 'Medium' | 'Low'>\",\n  \"ai_summary\": \"<A single sentence summarizing the day's overall security risk.>\",\n  \"total_critical_alerts\": <Total number of alerts with 'critical' severity>,\n  \"key_observations\": [\n    \"<Bulleted summary of the most significant activities or threat patterns, focused on business impact.>\",\n    \"<Another bulleted summary point.>\",\n    \"<And another, if necessary.>\"\n  ],\n  \"recommendation\": \"<A single, clear, actionable recommendation.>\"\n}\n\nHere is the raw alert data:\n{{ JSON.stringify($json) }}"
            },
            {
              "name": "apiKey"
            }
          ]
        },
        "options": {}
      },
      "id": "6a3c2d13-ea2c-4a21-8063-cee59b220746",
      "name": "Set Prompt for Summary",
      "type": "n8n-nodes-base.set",
      "position": [
        1856,
        544
      ],
      "typeVersion": 2
    },
    {
      "parameters": {
        "values": {
          "string": [
            {
              "name": "apiKey"
            },
            {
              "name": "chatInput",
              "value": "Review all security data from the last 24 hours. List all significant security alerts found. Your response MUST be a single, valid, minified JSON array of objects. Each object in the array should represent a distinct alert. If no significant alerts are found, return an empty array []."
            }
          ]
        },
        "options": {}
      },
      "id": "d27114b8-536c-4937-8af0-b6a4e0d20d9f",
      "name": "Set API Key & Initial Prompt",
      "type": "n8n-nodes-base.set",
      "position": [
        -288,
        560
      ],
      "typeVersion": 2
    },
    {
      "parameters": {
        "workflowId": {
          "__rl": true,
          "mode": "list",
          "value": "I0nUORqYTwDFZa51",
          "cachedResultName": "Get Real-Time Security Insights with NixGuard RAG and Wazuh Integration"
        },
        "workflowInputs": {
          "value": {},
          "schema": [],
          "mappingMode": "defineBelow",
          "matchingColumns": [],
          "attemptToConvertTypes": false,
          "convertFieldsToString": true
        },
        "options": {}
      },
      "id": "0adc2f21-8480-4749-9f0c-ef05b46b8b29",
      "name": "Execute: Get Daily Events as JSON (Get Real-Time Security Insights with NixGuard RAG and Wazuh Integration)",
      "type": "n8n-nodes-base.executeWorkflow",
      "position": [
        -32,
        560
      ],
      "typeVersion": 1.2
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "version": 2,
            "leftValue": "",
            "caseSensitive": true,
            "typeValidation": "strict"
          },
          "combinator": "and",
          "conditions": [
            {
              "id": "bb7ac757-8402-446e-9ee7-d0be89c769a7",
              "operator": {
                "type": "array",
                "operation": "exists",
                "singleValue": true
              },
              "leftValue": "={{ $json.alerts }}",
              "rightValue": ""
            }
          ]
        },
        "options": {}
      },
      "id": "40caac74-9e6c-4387-bc52-3bbe31e98481",
      "name": "If",
      "type": "n8n-nodes-base.if",
      "position": [
        480,
        560
      ],
      "typeVersion": 2.2
    },
    {
      "parameters": {
        "content": "## 💡 Workflow Overview\n\nThis workflow acts as an automated SOC analyst. It receives security alerts from & uses **NixGuard's AI** to analyze and prioritize them, and then routes them to the correct **Slack** channel based on the AI-assigned priority.\n\n**Use Case:** Eliminate alert fatigue by automatically distinguishing between critical threats that need immediate attention and informational logs that can be reviewed later.",
        "height": 260,
        "width": 520,
        "color": 7
      },
      "id": "f066f0e6-fa5d-4ab6-9d0c-0b6b5f9bb269",
      "name": "Workflow Overview",
      "type": "n8n-nodes-base.stickyNote",
      "position": [
        368,
        240
      ],
      "typeVersion": 1
    },
    {
      "parameters": {
        "values": {
          "string": [
            {
              "name": "ai_priority",
              "value": "={{ $json.ai_priority }}"
            },
            {
              "name": "ai_summary",
              "value": "={{ $json.ai_summary }}"
            }
          ]
        },
        "options": {}
      },
      "id": "c8cac8b0-dac8-42ed-bb38-9daba321de8e",
      "name": "Extract AI Priority & Summary",
      "type": "n8n-nodes-base.set",
      "position": [
        768,
        896
      ],
      "typeVersion": 2
    },
    {
      "parameters": {
        "workflowId": {
          "__rl": true,
          "mode": "list",
          "value": "I0nUORqYTwDFZa51",
          "cachedResultName": "Get Real-Time Security Insights with NixGuard RAG and Wazuh Integration"
        },
        "workflowInputs": {
          "value": {},
          "schema": [],
          "mappingMode": "defineBelow",
          "matchingColumns": [],
          "attemptToConvertTypes": false,
          "convertFieldsToString": true
        },
        "options": {}
      },
      "id": "d047a378-b41a-4652-83e8-85ed0e87a2d9",
      "name": "Execute: Generate Slack Message (Get Real-Time Security Insights with NixGuard RAG and Wazuh Integration)",
      "type": "n8n-nodes-base.executeWorkflow",
      "position": [
        2160,
        544
      ],
      "typeVersion": 1.2
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "7e0725fd-e9b4-4564-808a-708f6e8ecafa",
              "name": "output",
              "type": "array",
              "value": "={{ $json.alerts }}"
            }
          ]
        },
        "includeOtherFields": "={{ false }}",
        "options": {}
      },
      "id": "93849a07-6c84-4abc-b5f3-0025e0625187",
      "name": "Edit Fields",
      "type": "n8n-nodes-base.set",
      "position": [
        752,
        544
      ],
      "typeVersion": 3.4
    },
    {
      "parameters": {
        "jsCode": "try {\n  const alerts = $input.first().json.output;\n  \n  // Check if the result is a non-empty array.\n  if (Array.isArray(alerts) && alerts.length > 0) {\n    // Success! Return a separate item for each alert.\n    // The .map() function transforms the array of alert objects\n    // into an array of n8n items, which splits the execution.\n    return alerts.map(alert => ({ json: alert }));\n  }\n} catch (e) {\n  // This will catch errors if the cleaned string is still not valid JSON.\n  console.error(\"NixGuard did not return a valid JSON array even after cleaning:\", e);\n  console.error(\"String that failed to parse:\", jsonString);\n}\n\n// If parsing fails, it's not an array, or it's empty, return no items.\n// This will correctly stop this branch of the workflow.\nreturn [];"
      },
      "id": "4a49a959-6a27-410b-9a66-798480eb3612",
      "name": "Parse & Split Alerts",
      "type": "n8n-nodes-base.code",
      "position": [
        992,
        544
      ],
      "typeVersion": 2
    },
    {
      "parameters": {
        "aggregate": "aggregateAllItemData",
        "destinationFieldName": "output",
        "options": {}
      },
      "id": "c6b28204-a3c7-4b4e-9c3d-d5bb8fb4195b",
      "name": "Aggregate",
      "type": "n8n-nodes-base.aggregate",
      "position": [
        1568,
        560
      ],
      "typeVersion": 1
    },
    {
      "parameters": {
        "conditions": {
          "number": [
            {
              "value1": "={{ $json.level }}",
              "operation": "largerEqual",
              "value2": 7
            }
          ]
        }
      },
      "id": "079081a2-6e54-44cc-aee9-a00a34f545ef",
      "name": "Filter for Important Alerts (Level > 7)",
      "type": "n8n-nodes-base.if",
      "position": [
        1248,
        544
      ],
      "typeVersion": 1
    },
    {
      "parameters": {
        "otherOptions": {}
      },
      "id": "77ac98bf-68a8-4f3a-9614-ea46096a173b",
      "name": "Post CRITICAL Alert to Slack",
      "type": "n8n-nodes-base.slack",
      "position": [
        1312,
        800
      ],
      "webhookId": "b9db2ae0-4863-4809-82cd-0518807a21f6",
      "typeVersion": 2.3,
      "credentials": {
        "slackApi": {
          "id": "9bgF3Ztf4ga5VVdi",
          "name": "Slack account"
        }
      },
      "disabled": true
    },
    {
      "parameters": {
        "otherOptions": {}
      },
      "id": "af5aefb9-07e3-41a1-baa0-525ab53785b6",
      "name": "Post HIGH Alert to Slack",
      "type": "n8n-nodes-base.slack",
      "position": [
        1312,
        1040
      ],
      "webhookId": "b9db2ae0-4863-4809-82cd-0518807a21f6",
      "typeVersion": 2.3,
      "credentials": {
        "slackApi": {
          "id": "9bgF3Ztf4ga5VVdi",
          "name": "Slack account"
        }
      },
      "disabled": true
    },
    {
      "parameters": {
        "otherOptions": {}
      },
      "id": "5f26f03a-f715-4e0d-b99a-39df7ab2961d",
      "name": "Post INFO Alert to Slack",
      "type": "n8n-nodes-base.slack",
      "position": [
        1264,
        1344
      ],
      "webhookId": "b9db2ae0-4863-4809-82cd-0518807a21f6",
      "typeVersion": 2.3,
      "credentials": {
        "slackApi": {
          "id": "9bgF3Ztf4ga5VVdi",
          "name": "Slack account"
        }
      },
      "disabled": true
    },
    {
      "parameters": {
        "jsCode": "// Get the AI response. Your previous node used a complex path,\n// we assume the final AI string is here. Adjust if needed.\n// For example, it might be in $input.first().json.output or similar.\nconst rawOutput = $input.first().json.output;\n\nconst jsonString = rawOutput;\n\ntry {\n  // Parse the cleaned JSON string\n  const parsedResponse = JSON.parse(jsonString);\n  // Add the parsed fields to the n8n item.\n  // This merges the new data with any existing data from the input.\n  return [{ \n    json: {\n      ...$input.first().json,\n      ...parsedResponse \n    }\n  }];\n\n} catch (e) {\n  console.error(\"Failed to parse AI JSON response:\", e);\n  console.error(\"String that failed to parse:\", jsonString);\n  // Return an empty item to indicate failure and stop the flow if desired.\n  return [];\n}\n"
      },
      "id": "f3eb57ec-4b5f-4bd8-ab2f-2e1cab76a16e",
      "name": "Parse AI JSON Response",
      "type": "n8n-nodes-base.code",
      "position": [
        512,
        896
      ],
      "typeVersion": 2
    },
    {
      "parameters": {
        "rules": {
          "values": [
            {
              "conditions": {
                "options": {
                  "version": 2,
                  "leftValue": "",
                  "caseSensitive": true,
                  "typeValidation": "strict"
                },
                "combinator": "and",
                "conditions": [
                  {
                    "operator": {
                      "type": "string",
                      "operation": "equals"
                    },
                    "leftValue": "={{ $json.ai_priority }}",
                    "rightValue": "Critical"
                  }
                ]
              }
            },
            {
              "conditions": {
                "options": {
                  "version": 2,
                  "leftValue": "",
                  "caseSensitive": true,
                  "typeValidation": "strict"
                },
                "combinator": "and",
                "conditions": [
                  {
                    "id": "1be5b449-70dd-4bfe-91da-9ce25b523a74",
                    "operator": {
                      "name": "filter.operator.equals",
                      "type": "string",
                      "operation": "equals"
                    },
                    "leftValue": "={{ $json.ai_priority }}",
                    "rightValue": "High"
                  }
                ]
              }
            },
            {
              "conditions": {
                "options": {
                  "version": 2,
                  "leftValue": "",
                  "caseSensitive": true,
                  "typeValidation": "strict"
                },
                "combinator": "and",
                "conditions": [
                  {
                    "id": "1dd6239e-c965-4e06-85d9-4a8ad9f06287",
                    "operator": {
                      "name": "filter.operator.equals",
                      "type": "string",
                      "operation": "equals"
                    },
                    "leftValue": "={{ $json.ai_priority }}",
                    "rightValue": "Info"
                  }
                ]
              }
            },
            {
              "conditions": {
                "options": {
                  "version": 2,
                  "leftValue": "",
                  "caseSensitive": true,
                  "typeValidation": "strict"
                },
                "combinator": "and",
                "conditions": [
                  {
                    "id": "c019ac34-3196-49f1-8bdb-d399ff989103",
                    "operator": {
                      "name": "filter.operator.equals",
                      "type": "string",
                      "operation": "equals"
                    },
                    "leftValue": "={{ $json.ai_priority }}",
                    "rightValue": "Low"
                  }
                ]
              }
            }
          ]
        },
        "options": {}
      },
      "id": "17359ba5-d1b1-42a6-bdfd-ab93be0fe857",
      "name": "Switch",
      "type": "n8n-nodes-base.switch",
      "position": [
        1008,
        864
      ],
      "typeVersion": 3.2
    }
  ],
  "pinData": {},
  "connections": {
    "If": {
      "main": [
        [
          {
            "node": "Edit Fields",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Switch": {
      "main": [
        [
          {
            "node": "Post CRITICAL Alert to Slack",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Post HIGH Alert to Slack",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Post INFO Alert to Slack",
            "type": "main",
            "index": 0
          }
        ],
        []
      ]
    },
    "Aggregate": {
      "main": [
        [
          {
            "node": "Set Prompt for Summary",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Edit Fields": {
      "main": [
        [
          {
            "node": "Parse & Split Alerts",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Parse Alert Array": {
      "main": [
        [
          {
            "node": "If",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Run Daily at 8 AM": {
      "main": [
        [
          {
            "node": "Set API Key & Initial Prompt",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Parse & Split Alerts": {
      "main": [
        [
          {
            "node": "Filter for Important Alerts (Level > 7)",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Parse AI JSON Response": {
      "main": [
        [
          {
            "node": "Extract AI Priority & Summary",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Set Prompt for Summary": {
      "main": [
        [
          {
            "node": "Execute: Generate Slack Message (Get Real-Time Security Insights with NixGuard RAG and Wazuh Integration)",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Set API Key & Initial Prompt": {
      "main": [
        [
          {
            "node": "Execute: Get Daily Events as JSON (Get Real-Time Security Insights with NixGuard RAG and Wazuh Integration)",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Extract AI Priority & Summary": {
      "main": [
        [
          {
            "node": "Switch",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Filter for Important Alerts (Level > 7)": {
      "main": [
        [
          {
            "node": "Aggregate",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Execute: Generate Slack Message (Get Real-Time Security Insights with NixGuard RAG and Wazuh Integration)": {
      "main": [
        [
          {
            "node": "Parse AI JSON Response",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Execute: Get Daily Events as JSON (Get Real-Time Security Insights with NixGuard RAG and Wazuh Integration)": {
      "main": [
        [
          {
            "node": "Parse Alert Array",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": true,
  "settings": {},
  "versionId": "3fe1433b-beb5-4783-85e9-b814939ff8f1",
  "meta": {
    "templateId": "5896",
    "instanceId": "62a7b6a2e908df8acf4f496ee4d75eb0bdb27fe5e3b6a8646d7b010a58e8acdb"
  },
  "id": "JjkvnyIZMlzkzgKL",
  "tags": []
}
