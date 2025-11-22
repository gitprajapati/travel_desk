# Corporate Travel Agent - Travel Desk Management System

![Status](https://img.shields.io/badge/status-active-success)
![Python](https://img.shields.io/badge/python-3.9+-blue)
![FastAPI](https://img.shields.io/badge/fastapi-latest-green)
![License](https://img.shields.io/badge/license-proprietary-red)

## 🌍 Overview

A comprehensive **role-based AI-powered corporate travel management system** that streamlines travel requisition, approval workflows, and booking processes. Built with FastAPI, LangGraph, and Azure OpenAI, this system provides intelligent assistance for employees, managers, and travel desk personnel across an enterprise.

### Key Features

- 🤖 **AI-Powered Travel Assistant** - Natural language interface for travel requests and information
- 👥 **9 Role-Based Workflows** - Employee → IRM → SRM → BUH → SSUH → BGH → SSGH → CFO → Travel Desk
- 📋 **Travel Requisition Forms (TRF)** - Comprehensive draft, submit, and approval tracking
- ✈️ **Flight Management** - Search and booking with airline inventory, pricing tiers, and discounts
- 🏨 **Hotel Management** - Room inventory with dynamic pricing and amenity information
- 🔐 **JWT Authentication** - Secure role-based access control
- 💬 **Session Memory** - Multi-turn conversations with persistent chat history
- 📚 **RAG-Based Policy QA** - Intelligent answers to travel policy questions using document embeddings
- 📤 **Document Upload** - Policy and reference document management with background ingestion
- 💾 **PostgreSQL Backend** - Reliable data persistence with complex relationships

---

## 🚀 Quick Start

### Prerequisites

- Python 3.9+
- PostgreSQL database
- Azure OpenAI credentials
- pip or uv package manager

### Installation

```bash
# Clone the repository
git clone <repository-url>
cd travel_desk

# Install dependencies
uv pip install -r req.txt
# OR
pip install -r req.txt

# Configure environment
cp .env.example .env
# Edit .env with your credentials:
# - AZURE_API_KEY, AZURE_API_BASE, AZURE_MODEL
# - DATABASE_URL
# - JWT_SECRET
```

### Environment Variables

```env
# Azure OpenAI Configuration
AZURE_API_KEY=your-azure-openai-key
AZURE_API_BASE=your-azure-openai-endpoint
AZURE_API_VERSION=2024-02-15-preview
AZURE_MODEL=azure/gpt-4o-mini

# Database
DATABASE_URL=postgresql://user:password@localhost/travel_desk

# Security
JWT_SECRET=your-super-secret-key
JWT_ALGORITHM=HS256

# Storage
UPLOAD_STORAGE_DIR=docs/uploads

# Milvus Vector Database (for RAG)
MILVUS_HOST=localhost
MILVUS_PORT=19530

# Google Gemini (for embeddings)
GOOGLE_API_KEY=your-google-api-key
```

### Running the Application

```bash
# Start FastAPI server
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# Access API documentation
# Swagger UI: http://localhost:8000/docs
# ReDoc: http://localhost:8000/redoc
```

---

## 📖 API Quick Reference

### Authentication

All endpoints require a JWT token in the `Authorization` header:

```bash
curl -H "Authorization: Bearer <jwt_token>" http://localhost:8000/chat
```

### Core Endpoints

#### Chat Endpoint
```http
POST /chat
Content-Type: application/json
Authorization: Bearer <token>

{
  "message": "I need to travel to New York next month"
}
```

**Response:**
```json
{
  "success": true,
  "message": "I'd be happy to help you plan your travel to New York...",
  "chat_history": [...],
  "session_id": "EMP001",
  "data": {
    "role": "employee",
    "available_tools": ["create_trf_draft", "submit_trf", ...],
    "tool_count": 7,
    "message_count": 1
  }
}
```

---

## 👥 User Roles & Capabilities

### 1. **Employee**
- Create draft travel requisitions
- Submit travel requests for approval
- View personal travel history and status
- Query travel policies
- Save and edit drafts

### 2. **IRM** (Immediate Reporting Manager)
- View pending approvals
- Approve/reject travel requests
- Add approval comments
- View approval details and history

### 3. **SRM** (Senior Reporting Manager)
- Second-level approval authority
- Review IRM approvals
- Approve/reject with business justification
- View complete approval chain

### 4. **BUH** (Business Unit Head)
- Third-level approval with budget considerations
- Balance cost against unit budget
- Strategic review capability
- Approve/reject based on business needs

### 5. **SSUH** (Senior/Secondary Unit Head)
- Strategic unit-level review
- Ensure business alignment
- Approve/reject based on strategic fit

### 6. **BGH** (Business Group Head)
- Group-level leadership approval
- Policy compliance review
- Approve/reject based on group strategy

### 7. **SSGH** (Senior/Secondary Group Head)
- Senior leadership approval
- Company-wide strategic alignment
- Approve/reject based on company objectives

### 8. **CFO** (Chief Financial Officer)
- Final financial review
- High-value request validation
- Approve/reject based on financial impact

### 9. **Travel Desk**
- Approve travel arrangements for execution
- Search and book flights
- Search and book hotels
- Complete travel plans
- Track all applications
- Manage travel inventory

---

## 🏗️ System Architecture

### Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Framework** | FastAPI | RESTful API with async support |
| **LLM Orchestration** | LangGraph | Multi-turn workflow with tool calling |
| **Language Model** | Azure OpenAI | GPT-4 for intelligent responses |
| **Embeddings** | Google Gemini | Document embeddings for RAG |
| **Vector DB** | Milvus | Semantic search for policies |
| **Database** | PostgreSQL | Relational data persistence |
| **Authentication** | JWT | Secure token-based auth |
| **Task Queue** | FastAPI BackgroundTasks | Async document ingestion |

### Project Structure

```
.
├── main.py                    # FastAPI application entry point
├── models.py                  # SQLAlchemy database models
├── jwt_gen.py                 # JWT token generation utility
├── req.txt                    # Python dependencies
├── sql_script.sql             # Database initialization
├── agent/
│   ├── workflow.py            # Role-based travel agent with LangGraph
│   ├── tools.py               # LangChain tools for TRF/booking operations
│   ├── llm.py                 # LLM configuration
│   ├── schema.py              # Pydantic request/response schemas
│   └── policy_tool.py         # Policy QA tool implementation
├── src/
│   ├── config/
│   │   └── settings.py        # Environment configuration
│   └── rag/
│       ├── config/
│       │   └── rag_config.py  # RAG configuration
│       ├── embeddings/
│       │   └── gemini_embedder.py  # Google Gemini embeddings
│       ├── models/
│       │   └── rag_models.py  # RAG-specific data models
│       └── retrieval/
│           ├── milvus_retriever.py # Milvus vector search
│           ├── policy_qa.py    # QA system
│           └── chunk_metadata.py  # Chunk storage
├── scripts/
│   ├── ingest_policies.py     # Document ingestion pipeline
│   └── test_rag.py            # RAG testing utilities
└── docs/
    └── uploads/               # Uploaded policy documents
```

---

## 🔄 Approval Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                     APPROVAL WORKFLOW                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  DRAFT         PENDING_IRM      PENDING_SRM      PENDING_BUH  │
│    │               │                │                │          │
│    └──────────────►│───────────────►│───────────────►│          │
│                    IRM              SRM              BUH         │
│                                                      │          │
│                                          PENDING_SSUH│          │
│                                              │       │          │
│                                              └───────┼─────┐    │
│                                                  SSUH│     │    │
│                                                      │     │    │
│                                           PENDING_BGH│     │    │
│                                               │      │     │    │
│                                               └──────┼─────┼────┐
│                                                 BGH  │     │    │
│                                                      │     │    │
│                                          PENDING_SSGH│     │    │
│                                              │       │     │    │
│                                              └───────┼─────┼────┼──┐
│                                                 SSGH │     │    │  │
│                                                      │     │    │  │
│                                            PENDING_CFO│     │    │  │
│                                                │      │     │    │  │
│                                                └──────┼─────┼────┼──┼──┐
│                                                  CFO  │     │    │  │  │
│                                                       │     │    │  │  │
│                                      PENDING_TRAVEL_DESK     │  │  │  │
│                                               │       │     │   │  │  │
│                                               └───────┼─────┼───┼──┼──┼──┐
│                                            Travel Desk│     │   │  │  │  │
│                                                       │     │   │  │  │  │
│                                                  COMPLETED   │   │  │  │  │
│                                                             │   │  │  │  │
│                   REJECTED (at any stage) ◄───────────────┴───┴──┴──┴──┴──
│                       │
│                       └─ Cannot proceed further; needs re-submission
│
```

---

## 🛠️ Development Guide

### Adding New Tools

1. **Define schema** in `agent/schema.py`:
```python
class MyToolInput(BaseModel):
    param1: str
    param2: int
```

2. **Create tool** in `agent/tools.py`:
```python
@tool(args_schema=MyToolInput)
def my_tool(param1: str, param2: int) -> str:
    """Description of tool."""
    # Implementation
    return result
```

3. **Add to tools list** in `agent/tools.py`:
```python
ALL_TOOLS = [
    create_trf_draft,
    my_tool,  # Add here
    # ... other tools
]
```

4. **Update role mappings** in `agent/workflow.py`:
```python
ROLE_TOOLS_MAP = {
    "employee": ["create_trf_draft", "my_tool", ...],
    # ...
}
```

### Database Migrations

```bash
# Initialize database
python -c "from models import Base; Base.metadata.create_all(bind=engine)"

# Seed reference data
python seed.py

# Run migrations from SQL script
psql -d travel_desk -f sql_script.sql
```

---

## 📊 Data Models Overview

### Travel Requisition Form (TRF)
Central entity tracking travel requests through the approval chain.

**Key Fields:**
- `trf_number`: Unique identifier (DRAFT-TRF202500001, TRF202500001)
- `employee_*`: Employee information (embedded, not foreign key)
- `status`: Current workflow stage
- `travel_type`: DOMESTIC or INTERNATIONAL
- `estimated_cost`: Budget estimate
- `approval_chain`: Timestamps and comments for each approver

### Travel Bookings
Represents confirmed travel arrangements (flights and hotels).

**Related Entities:**
- `FlightBooking`: Flight reservations with PNR
- `HotelBooking`: Room reservations with confirmation numbers
- `FlightInventory`: Available flights with pricing by cabin class
- `HotelRoomInventory`: Available rooms with dynamic pricing

---

## 🔐 Security Considerations

1. **JWT Authentication**: All endpoints require valid JWT tokens with required claims
2. **Role-Based Access Control**: Tools dynamically filtered by user role
3. **Session Isolation**: Users can only access their own sessions
4. **Tool Whitelisting**: Strict enforcement of allowed tools per role
5. **Input Validation**: Pydantic schemas validate all inputs
6. **Error Handling**: Secure error responses without sensitive data leaks

---

## 📚 Testing

### Test RAG System

```bash
python scripts/test_rag.py
```

### Ingest Sample Policies

```bash
python scripts/ingest_policies.py docs/sample_policies/*.md
```

### Generate Test JWT Token

```bash
python jwt_gen.py
```

---

## 🐛 Troubleshooting

### Common Issues

**Issue**: `DATABASE_URL not set`
- **Solution**: Ensure `.env` file contains valid `DATABASE_URL`

**Issue**: `Azure OpenAI API errors`
- **Solution**: Check `AZURE_API_KEY`, `AZURE_API_BASE`, `AZURE_MODEL`

**Issue**: `Milvus connection failed`
- **Solution**: Verify Milvus is running: `docker ps | grep milvus`

**Issue**: `Tool not available for role`
- **Solution**: Check `ROLE_TOOLS_MAP` in `agent/workflow.py`

---

## 📞 Support & Contribution

For issues, feature requests, or contributions:
1. Check existing documentation in `docs/`
2. Review code comments and docstrings
3. Consult technical documentation in `TECHNICAL.md`
4. Review business documentation in `BUSINESS_DOC.md`

---

## 📄 License

Proprietary - All Rights Reserved

---

## 🔗 Related Documentation

- **[Business Documentation](./BUSINESS_DOC.md)** - Business requirements, workflows, and user guide
- **[Technical Documentation](./TECHNICAL.md)** - Architecture, API reference, and development guide

---

**Last Updated**: November 2025  
**Maintainers**: YASH Technologies Travel Desk Team
