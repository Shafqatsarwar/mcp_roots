Project Context Discovery with MCP Roots
Roots are a way to grant MCP servers access to specific files and folders on your local machine. Think of them as a permission system that says "Hey, MCP server, you can access these files" - but they do much more than just grant permission.

The Problem
Development tools often need to understand the project structure they're working with:

Which files belong to the project?
Where are source files located?
What are the project boundaries?
Traditionally, this required manual configuration:

project_dir = "/path/to/project"  # Hardcoded or configured
analyze_code(f"{project_dir}/src/main.py")
Without roots, you'd run into a common issue. Imagine you have an MCP server with a video conversion tool that takes a file path and converts an MP4 to MOV format.

When a user asks Claude to "convert biking.mp4 to mov format", Claude would call the tool with just the filename. But here's the problem - Claude has no way to search through your entire file system to find where that file actually lives.

Your file system might be complex with files scattered across different directories. The user knows the biking.mp4 file is in their Movies folder, but Claude doesn't have that context.

You could solve this by requiring users to always provide full paths, but that's not very user-friendly. Nobody wants to type out complete file paths every time.

The Solution: MCP Roots
MCP Roots provides automatic project context discovery:

Clients (IDEs, editors) expose project directories
Servers (tools, analyzers) request access as needed
No manual configuration required
# Server just requests roots and gets project context
roots = await ctx.session.list_roots()
project_files = roots.roots[0].list_files("**/*.py")
Roots in Action
Here's how the workflow changes with roots:

User asks to convert a video file
Agent calls list_roots to see what directories it can access
Agent calls read_dir on accessible directories to find the file
Once found, Agent calls the conversion tool with the full path
This happens automatically - users can still just say "convert biking.mp4" without providing full paths.

🛠️ Implementation
Client Side
The client needs to:

Implement the roots capability
Handle roots/list requests
Provide project directory information
See client.py for a basic implementation.

Server Side
The server can:

Request project roots from client
Use roots to analyze project structure
Work with files in project context
See server.py for a basic implementation.

🔍 Key Concepts
1. Project Roots
Definition: Directories that form the boundaries of a project
Examples:
Workspace folders in VS Code
Project directories in PyCharm
Git repository root directories
2. URI Representation
# Example root structure
{
    "uri": "file:///home/user/projects/myapp",
    "name": "MyApp Project"
}
3. Client-Server Flow

💻 Hands-On Implementation
Step 1: Setup Project
cd mcp_code
uv sync
Step 2: Run the Demo
Terminal 1:

uv run uvicorn server:mcp_app --reload
Terminal 2:

uv run python client.py
Step 3: Observe the Output
You'll see:

Client initialization with roots capability
Server requesting project roots
Project analysis results showing:
File counts by type
Project features detection
Basic structure analysis
📚 Further Reading
MCP Roots Specification
Project Context in Development Tools
Best Practices for Root Handling
Remember: The power of MCP roots lies in enabling tools to understand and work with project context automatically, making development more efficient and context-aware.