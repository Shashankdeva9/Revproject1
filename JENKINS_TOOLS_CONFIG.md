# Jenkins Tools Configuration - Step by Step

## Why Configure Tools?

Your Jenkinsfile uses:
```groovy
tools {
    maven 'Maven3'    // ← Must match name in Jenkins
    jdk 'JDK17'       // ← Must match name in Jenkins
}
```

Jenkins needs to know WHERE these tools are or HOW to install them.

---

## Step-by-Step Configuration

### 1. Open Tools Configuration

```
Jenkins Dashboard 
  → Click "Manage Jenkins" (left sidebar)
  → Click "Tools" (or "Global Tool Configuration")
```

You'll see a page with sections for different tools.

---

### 2. Configure Maven

**Scroll down to "Maven installations" section**

Click **"Add Maven"** button

Fill in:
```
Name: Maven3                    ← MUST match Jenkinsfile
☑ Install automatically         ← Check this box
Version: 3.9.5                  ← Select from dropdown
```

**What this does:**
- Jenkins will auto-download Maven 3.9.5 when first needed
- Stores it in Jenkins workspace
- No manual installation required

---

### 3. Configure JDK

**Scroll down to "JDK installations" section**

Click **"Add JDK"** button

Fill in:
```
Name: JDK17                     ← MUST match Jenkinsfile
☑ Install automatically         ← Check this box
Install from: adoptium.net      ← Select from dropdown
Version: jdk-17.0.9+9          ← Select Java 17
```

**What this does:**
- Jenkins will auto-download JDK 17 when first needed
- Sets JAVA_HOME automatically
- No manual installation required

---

### 4. Save Configuration

Click **"Save"** or **"Apply"** at bottom of page

---

## Alternative: Use Existing Installations

If you already have Maven/Java installed on your system:

### Maven (Manual):
```
Name: Maven3
☐ Install automatically         ← Uncheck
MAVEN_HOME: C:\Program Files\Maven    (Windows)
MAVEN_HOME: /usr/share/maven          (Linux)
```

### JDK (Manual):
```
Name: JDK17
☐ Install automatically         ← Uncheck
JAVA_HOME: C:\Program Files\Java\jdk-17    (Windows)
JAVA_HOME: /usr/lib/jvm/java-17-openjdk   (Linux)
```

---

## Verify Configuration

After saving, your Jenkinsfile will work because:

```groovy
tools {
    maven 'Maven3'    // ✅ Finds "Maven3" configuration
    jdk 'JDK17'       // ✅ Finds "JDK17" configuration
}
```

When pipeline runs:
1. Jenkins checks if Maven3 exists
2. If not, downloads and installs it
3. Sets PATH to use Maven3
4. Same for JDK17
5. Runs: `mvn clean package`

---

## Common Errors

### ❌ "Maven3 not found"
**Cause:** Name mismatch
**Fix:** Tool name in Jenkins MUST exactly match Jenkinsfile

### ❌ "JAVA_HOME not set"
**Cause:** JDK not configured
**Fix:** Add JDK17 in Tools configuration

---

## Quick Test

After configuration, run in Jenkins Script Console:
```groovy
// Manage Jenkins → Script Console
def maven = Jenkins.instance.getDescriptor("hudson.tasks.Maven")
println "Maven installations: ${maven.installations*.name}"

def jdk = Jenkins.instance.getDescriptor("hudson.model.JDK")
println "JDK installations: ${jdk.installations*.name}"
```

Should output:
```
Maven installations: [Maven3]
JDK installations: [JDK17]
```

✅ Configuration Complete!
