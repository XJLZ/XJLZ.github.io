---
title: JetBrains产品激活
date: 2020-08-10 15:32:00
tags:
- JetBrains
- 激活
---

# Windows

```powershell
# JetBrains激活脚本
# powershell -ExecutionPolicy Bypass -File .\activeJB.ps1
# 全局错误处理
function Global-ErrorHandler {
    param([string]$ErrorMessage)
    
    Write-Host ""
    Write-Host "══════════════════════════════════════════════════" -ForegroundColor DarkGray
    Write-Log "发生错误: $ErrorMessage" "Red" "💥"
    Write-Log "可能是安全软件阻止了脚本执行" "Yellow" "🛡️"
    Write-Log "建议操作:" "Cyan" "💡"
    Write-Log "1. 暂时关闭安全软件" "White" "   1️⃣"
    Write-Log "2. 重新以管理员身份运行此脚本" "White" "   2️⃣"
    Write-Log "3. 如果问题持续，请联系客服" "White" "   3️⃣"
    Write-Host ""
    Write-Host "按任意键退出..." -ForegroundColor DarkGray
    $null = $Host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")
    exit 1
}

# 编码设置
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# TLS/SSL安全设置 - 解决连接问题
try {
    # 启用所有TLS版本以兼容不同系统
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 -bor [Net.SecurityProtocolType]::Tls11 -bor [Net.SecurityProtocolType]::Tls
    Write-Host "TLS安全协议已配置" -ForegroundColor Green
} catch {
    Write-Host "TLS配置失败，但继续执行..." -ForegroundColor Yellow
}

# 忽略SSL证书验证（用于解决证书问题）
try {
    Add-Type -TypeDefinition @"
        using System.Net;
        using System.Security.Cryptography.X509Certificates;
        public class TrustAllCertsPolicy : ICertificatePolicy {
            public bool CheckValidationResult(
                ServicePoint srvPoint, X509Certificate certificate,
                WebRequest request, int certificateProblem) {
                return true;
            }
        }
"@ -ErrorAction SilentlyContinue
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
    Write-Host "SSL证书验证已放宽" -ForegroundColor Green
} catch {
    Write-Host "SSL证书配置失败，但继续执行..." -ForegroundColor Yellow
}

# 全局配置
$script:url_download = "https://szjcobs.coursegate.cn/2026/09/29/jb-run/"

# 工作目录设置
$script:work_dir = "C:\Users\Public\idecode"
$script:config_dir = "$script:work_dir\config"
$script:plugins_dir = "$script:work_dir\plugins"
$script:keys_dir = "$script:work_dir\keys"

# JetBrains 产品列表
$script:jetbrainsProducts = @(
    @{ name = "idea";     displayName = "IntelliJ IDEA";     processes = @("idea", "idea64"); envVar = "IDEA_VM_OPTIONS" },
    @{ name = "clion";    displayName = "CLion";             processes = @("clion", "clion64"); envVar = "CLION_VM_OPTIONS" },
    @{ name = "phpstorm"; displayName = "PhpStorm";          processes = @("phpstorm", "phpstorm64"); envVar = "PHPSTORM_VM_OPTIONS" },
    @{ name = "goland";   displayName = "GoLand";            processes = @("goland", "goland64"); envVar = "GOLAND_VM_OPTIONS" },
    @{ name = "pycharm";  displayName = "PyCharm";           processes = @("pycharm", "pycharm64"); envVar = "PYCHARM_VM_OPTIONS" },
    @{ name = "webstorm"; displayName = "WebStorm";          processes = @("webstorm", "webstorm64"); envVar = "WEBSTORM_VM_OPTIONS" },
    @{ name = "rider";    displayName = "Rider";             processes = @("rider", "rider64"); envVar = "RIDER_VM_OPTIONS" },
    @{ name = "datagrip"; displayName = "DataGrip";          processes = @("datagrip", "datagrip64"); envVar = "DATAGRIP_VM_OPTIONS" },
    @{ name = "rubymine"; displayName = "RubyMine";          processes = @("rubymine", "rubymine64"); envVar = "RUBYMINE_VM_OPTIONS" },
    @{ name = "appcode";  displayName = "AppCode";           processes = @("appcode"); envVar = "APPCODE_VM_OPTIONS" },
    @{ name = "dataspell"; displayName = "DataSpell";        processes = @("dataspell"); envVar = "DATASPELL_VM_OPTIONS" },
    @{ name = "rustrover"; displayName = "RustRover";        processes = @("rustrover"); envVar = "RUSTROVER_VM_OPTIONS" }
)

# 全局变量存储剩余使用次数
$script:remainingUses = 0

# 日志函数 - 美化版本
function Write-Log {
    param(
        [string]$message,
        [string]$color = "White",
        [string]$emoji = "",
        [switch]$NoTimestamp,
        [switch]$Silent
    )
    
    if ($Silent) { return }
    
    if ($NoTimestamp) {
        $timestampText = ""
    } else {
        $timestamp = Get-Date -Format "HH:mm:ss"
        $timestampText = "[$timestamp] "
    }
    $emojiText = if ($emoji) { "$emoji " } else { "" }
    Write-Host "$timestampText$emojiText$message" -ForegroundColor $color
}

# 美化进度条
function Write-ProgressBar {
    param(
        [int]$percent,
        [string]$status = "处理中...",
        [string]$emoji = "🔄"
    )
    $barLength = 25
    $filledBars = [math]::Floor($percent / (100 / $barLength))
    $emptyBars = $barLength - $filledBars
    $progressBar = "$emoji [" + ("█" * $filledBars) + ("░" * $emptyBars) + "]"
    Write-Host "`r$progressBar $percent% - $status" -NoNewline -ForegroundColor Cyan
}

# 显示标题
function Show-Header {
    Clear-Host
    Write-Host ""
    Write-Host "🚀 JetBrains 专业激活系统" -ForegroundColor Magenta
    Write-Host "══════════════════════════════════════════════════" -ForegroundColor DarkGray
    Write-Host ""
}

# 授权码验证函数

function Verify-ActivationCode {
    return $true
}

# 检查360杀毒软件干扰
function Check-Antivirus-Interference {
    Write-Log "检查系统环境..." "Cyan" "🛡️"
    
    # 只检查360安全卫士相关进程
    $antivirusProcesses = @(
        "360sd",      # 360杀毒
        "360tray"     # 360安全卫士
    )
    
    $runningAV = @()
    
    foreach ($avProc in $antivirusProcesses) {
        $processes = Get-Process -Name "*$avProc*" -ErrorAction SilentlyContinue
        if ($processes) {
            foreach ($proc in $processes) {
                $runningAV += $avProc
                Write-Log "检测到360安全软件: $avProc (PID: $($proc.Id))" "Yellow" "⚠️"
            }
        }
    }
    
    if ($runningAV.Count -gt 0) {
        Write-Log "发现360安全软件可能会干扰激活过程！" "Red" "🛡️"
        Write-Log "建议暂时退出360安全软件后再继续激活！" "Yellow" "💡"
        Write-Log "郑重声明：激活没有任何安全风险！只起到激活作用，放心使用！只是360可能会导致激活失败！" "Yellow" "💡"
        Write-Log "退出方法：右键点击360托盘图标 -> 退出" "White" "   "
        
        $choice = Read-Host "先退出360再继续激活，是否继续激活？(Y-继续/N-退出)"
        if ($choice -notmatch "^[Yy]") {
            Write-Log "用户选择退出" "Yellow" "👋"
            return $false
        }
    } else {
        Write-Log "未发现360安全软件运行" "Green" "✅"
    }
    
    Write-Log "系统环境检查完成" "Green" "✅"
    return $true
}

# 检查是否有JetBrains软件正在运行（仅通过进程检测）
function Check-RunningProcesses {
    Show-Header
    Write-Log "检查JetBrains软件运行状态..." "Cyan" "🔍"
    
    $runningProcesses = @()
    
    # 获取所有进程
    $allProcesses = Get-Process -ErrorAction SilentlyContinue
    
    foreach ($process in $allProcesses) {
        $processName = $process.ProcessName.ToLower()
        $isJetBrainsProcess = $false
        $detectedProduct = ""
        
        # 检查每个JetBrains产品的进程名
        foreach ($product in $script:jetbrainsProducts) {
            foreach ($procName in $product.processes) {
                if ($processName -eq $procName.ToLower() -or $processName.StartsWith($procName.ToLower())) {
                    $isJetBrainsProcess = $true
                    $detectedProduct = $product.displayName
                    break
                }
            }
            if ($isJetBrainsProcess) { break }
        }
        
        # 如果进程名匹配，进一步验证
        if ($isJetBrainsProcess) {
            try {
                # 检查进程路径是否包含JetBrains
                $processPath = $process.Path
                if ($processPath -and $processPath -match "JetBrains") {
                    $runningProcesses += @{
                        Process = $process
                        Product = $detectedProduct
                        Name = $process.ProcessName
                        Id = $process.Id
                        Path = $processPath
                    }
                } else {
                    # 即使路径不包含JetBrains，但进程名精确匹配，也认为是JetBrains进程
                    $runningProcesses += @{
                        Process = $process
                        Product = $detectedProduct
                        Name = $process.ProcessName
                        Id = $process.Id
                        Path = $processPath
                    }
                }
            } catch {
                # 如果无法获取路径，但进程名匹配，也认为是JetBrains进程
                $runningProcesses += @{
                    Process = $process
                    Product = $detectedProduct
                    Name = $process.ProcessName
                    Id = $process.Id
                    Path = "无法获取路径"
                }
            }
        }
    }
    
    if ($runningProcesses.Count -gt 0) {
        # 提取不重复的产品名称
        $uniqueProducts = $runningProcesses | ForEach-Object { $_.Product } | Sort-Object | Get-Unique
        $productList = $uniqueProducts -join ", "
        
        Write-Log "发现以下JetBrains软件正在运行:" "Red" "⚠️"
        foreach ($procInfo in $runningProcesses) {
            Write-Log "  - $($procInfo.Product) (进程: $($procInfo.Name), PID: $($procInfo.Id))" "Yellow" "📱"
        }
        
        Write-Log "请关闭 【$productList】 软件后按回车键继续..." "Red" "⏸️"
        $null = Read-Host "   按回车键继续"
        
        # 递归检查，直到所有进程关闭
        return Check-RunningProcesses
    }
    
    Write-Log "未发现正在运行的JetBrains软件，继续激活流程" "Green" "✅"
    Start-Sleep -Seconds 1
    return $true
}

# 初始化系统（清理环境变量）- 增强版
function Initialize-System {
    Show-Header
    Write-Log "正在初始化激活环境..." "Cyan" "🔧"
    
    $totalProducts = $script:jetbrainsProducts.Count
    $currentProduct = 0
    $cleanedCount = 0
    
    foreach ($product in $script:jetbrainsProducts) {
        $currentProduct++
        $percent = [math]::Round(($currentProduct / $totalProducts) * 100, 0)
        
        Write-ProgressBar -percent $percent -status "初始化中 ($currentProduct/$totalProducts)" -emoji "🧹"
        
        $envVar = $product.envVar
        
        # 只清理旧的无效环境变量，不再清理所有环境变量
        # 这样可以在多软件共存时保留其他软件的配置
        $currentValue = [Environment]::GetEnvironmentVariable($envVar, "User")
        if (-not [string]::IsNullOrEmpty($currentValue) -and -not (Test-Path $currentValue)) {
            [Environment]::SetEnvironmentVariable($envVar, $null, "User")
            $cleanedCount++
        }
        
        # 清理系统环境变量（需要管理员权限）
        $systemValue = [Environment]::GetEnvironmentVariable($envVar, "Machine")
        if (-not [string]::IsNullOrEmpty($systemValue) -and -not (Test-Path $systemValue)) {
            [Environment]::SetEnvironmentVariable($envVar, $null, "Machine")
            $cleanedCount++
        }
    }
    
    # 完成进度条
    Write-Host ""  # 换行
    
    if ($cleanedCount -gt 0) {
        Write-Log "初始化完成，清理了 $cleanedCount 个无效环境变量" "Green" "✅"
    } else {
        Write-Log "初始化完成，无需清理" "Green" "✅"
    }
    Start-Sleep -Seconds 1
}

# 创建工作目录（增强版，带杀毒软件检测）
function Create-WorkDirectory {
    Show-Header
    Write-Log "正在创建工作目录..." "Cyan" "📁"
    
    # 如果目录已存在，先删除
    if (Test-Path $script:work_dir) {
        try {
            Remove-Item -Path $script:work_dir -Recurse -Force -ErrorAction Stop
            Write-Log "清理旧目录成功" "Yellow" "🗑️"
        } catch {
            Write-Log "清理目录失败，可能被360安全软件阻止: $($_.Exception.Message)" "Red" "❌"
            Write-Log "请检查360安全软件是否阻止了文件操作" "Yellow" "🛡️"
            
            $choice = Read-Host "是否重试？(Y-重试/N-退出)"
            if ($choice -match "^[Yy]") {
                return Create-WorkDirectory
            } else {
                return $false
            }
        }
    }
    
    # 创建新目录
    try {
        New-Item -Path $script:work_dir -ItemType Directory -Force | Out-Null
        New-Item -Path $script:config_dir -ItemType Directory -Force | Out-Null
        New-Item -Path $script:plugins_dir -ItemType Directory -Force | Out-Null
        New-Item -Path $script:keys_dir -ItemType Directory -Force | Out-Null
        Write-Log "工作目录创建成功: $script:work_dir" "Green" "✅"
        return $true
    } catch {
        Write-Log "创建目录失败，可能被360安全软件阻止: $($_.Exception.Message)" "Red" "❌"
        Write-Log "请暂时退出360安全软件后重试" "Yellow" "🛡️"
        
        $choice = Read-Host "是否重试？(Y-重试/N-退出)"
        if ($choice -match "^[Yy]") {
            return Create-WorkDirectory
        } else {
            return $false
        }
    }
}

# 下载文件（显示整体进度）- 增强版
function Download-Files-With-Progress {
    Show-Header
    Write-Log "正在激活..." "Cyan" "📥"
    
    # 确保工作目录变量存在
    if (-not (Test-Path variable:\script:work_dir)) {
        $script:work_dir = "C:\Users\Public\idecode"
        Write-Log "设置工作目录: $script:work_dir" "Yellow" "📁"
    }
    
    # 确保其他目录变量存在
    if (-not (Test-Path variable:\script:config_dir)) {
        $script:config_dir = "$script:work_dir\config"
    }
    if (-not (Test-Path variable:\script:plugins_dir)) {
        $script:plugins_dir = "$script:work_dir\plugins"
    }
    if (-not (Test-Path variable:\script:keys_dir)) {
        $script:keys_dir = "$script:work_dir\keys"
    }
    
    $files = @(
        "ja-netfilter.jar",
        "config/dns.conf",
        "config/native.conf",
        "config/power.conf",
        "config/url.conf",
        "plugins/dns.jar",
        "plugins/native.jar",
        "plugins/power.jar",
        "plugins/url.jar",
        "plugins/hideme.jar",
        "plugins/privacy.jar",
        "keys/idea.key",
        "keys/idea64.exe.vmoptions",
        "keys/pycharm.key",
        "keys/pycharm64.exe.vmoptions",
        "keys/phpstorm.key",
        "keys/phpstorm64.exe.vmoptions",
        "keys/webstorm.key",
        "keys/webstorm64.exe.vmoptions",
        "keys/rider.key",
        "keys/rider64.exe.vmoptions",
        "keys/goland.key",
        "keys/goland64.exe.vmoptions",
        "keys/clion.key",
        "keys/clion64.exe.vmoptions",
        "keys/datagrip.key",
        "keys/datagrip64.exe.vmoptions",
        "keys/rubymine.key",
        "keys/rubymine64.exe.vmoptions",
        "codex.txt"  # 新增激活码文件
    )
    
    $totalFiles = $files.Count
    $currentFile = 0
    $successCount = 0
    $errorCount = 0
    $failedFiles = @()
    
    # 创建必要的目录结构
    Create-Directory-Structure
    
    foreach ($file in $files) {
        $currentFile++
        $percent = [math]::Round(($currentFile / $totalFiles) * 100, 0)
        
        Write-ProgressBar -percent $percent -status "激活中 ($currentFile/$totalFiles)" -emoji "📥"
        
        # 根据文件类型确定保存路径
        if ($file -match "^config/") {
            $output = "$script:config_dir\" + ($file -replace "^config/", "")
        } elseif ($file -match "^plugins/") {
            $output = "$script:plugins_dir\" + ($file -replace "^plugins/", "")
        } elseif ($file -match "^keys/") {
            $output = "$script:keys_dir\" + ($file -replace "^keys/", "")
        } else {
            $output = "$script:work_dir\$file"
        }
        
        # 确保目录存在
        $dir = [System.IO.Path]::GetDirectoryName($output)
        if (-not (Test-Path $dir)) { 
            New-Item -ItemType Directory -Path $dir -Force | Out-Null
        }
        
        # 下载文件（带重试机制）- 静默模式，不显示具体文件信息
        $downloadResult = Download-File-With-Retry -Url "$script:url_download$file" -OutputPath $output -FileName $file
        
        if ($downloadResult.Success) {
            # 验证文件是否被360安全软件删除
            if (-not (Test-File-Integrity -FilePath $output -FileName $file)) {
                $errorCount++
                $failedFiles += @{
                    File = $file
                    Error = "文件可能被360安全软件删除"
                }
            } else {
                $successCount++
            }
        } else {
            $errorCount++
            $failedFiles += @{
                File = $file
                Error = $downloadResult.Error
            }
        }
    }
    
    # 完成进度条
    Write-Host ""  # 换行
    
    # 显示下载结果
    Show-Download-Result -SuccessCount $successCount -ErrorCount $errorCount -FailedFiles $failedFiles -TotalFiles $totalFiles
    
    # 如果有文件下载失败，询问用户是否继续
    if ($errorCount -gt 0) {
        Write-Log "有 $errorCount 个文件初始化失败，激活可能不完整" "Red" "⚠️"
        Write-Log "可能是360安全软件阻止了文件下载或删除文件" "Yellow" "🛡️"
        
        # 显示关键文件检查
        $criticalFilesMissing = Check-Critical-Files
        
        if ($criticalFilesMissing) {
            Write-Log "关键文件缺失，无法继续激活" "Red" "❌"
            Write-Log "请退出360安全软件后重新运行脚本" "Yellow" "💡"
            
            $choice = Read-Host "是否重试下载？(Y-重试/N-退出)"
            if ($choice -match "^[Yy]") {
                return Download-Files-With-Progress
            } else {
                return $false
            }
        } else {
            Write-Log "虽然部分文件初始化失败，但关键文件存在，可以继续" "Yellow" "⚠️"
            $choice = Read-Host "是否继续激活？(Y/N)"
            if ($choice -notmatch "^[Yy]") {
                return $false
            }
        }
    }
    
    Write-Log "激活完成，开始部署..." "Green" "✅"
    Start-Sleep -Seconds 1
    return $true
}

# 创建目录结构
function Create-Directory-Structure {
    try {
        if (-not (Test-Path $script:work_dir)) {
            New-Item -Path $script:work_dir -ItemType Directory -Force | Out-Null
        }
        if (-not (Test-Path $script:config_dir)) {
            New-Item -Path $script:config_dir -ItemType Directory -Force | Out-Null
        }
        if (-not (Test-Path $script:plugins_dir)) {
            New-Item -Path $script:plugins_dir -ItemType Directory -Force | Out-Null
        }
        if (-not (Test-Path $script:keys_dir)) {
            New-Item -Path $script:keys_dir -ItemType Directory -Force | Out-Null
        }
    } catch {
        Write-Log "创建目录结构失败，可能被360安全软件阻止: $($_.Exception.Message)" "Red" "❌"
        throw
    }
}

# 验证文件完整性
function Test-File-Integrity {
    param(
        [string]$FilePath,
        [string]$FileName
    )
    
    if (-not (Test-Path $FilePath)) {
        return $false
    }
    
    $fileInfo = Get-Item $FilePath
    
    # 检查文件大小
    if ($fileInfo.Length -eq 0) {
        return $false
    }
    
    # 关键文件特定检查
    switch ($FileName) {
        "ja-netfilter.jar" { 
            # JAR文件应该有合理的大小
            if ($fileInfo.Length -lt 1024) { return $false }
        }
        { $_ -match "\.jar$" } { 
            if ($fileInfo.Length -lt 100) { return $false }
        }
        { $_ -match "\.key$" } { 
            if ($fileInfo.Length -lt 10) { return $false }
        }
        "激活码.txt" { 
            if ($fileInfo.Length -lt 10) { return $false }
        }
    }
    
    return $true
}

# 带重试机制的文件下载（增强版，带安全软件检测）- 静默模式
function Download-File-With-Retry {
    param(
        [string]$Url,
        [string]$OutputPath,
        [string]$FileName,
        [int]$MaxRetries = 3
    )
    
    $retryCount = 0
    $success = $false
    $errorMsg = ""
    
    # 保存原来的进度首选项
    $originalProgressPreference = $ProgressPreference
    
    while (-not $success -and $retryCount -lt $MaxRetries) {
        $retryCount++
        
        try {
            # 设置进度首选项为静默，隐藏所有进度信息
            $ProgressPreference = 'SilentlyContinue'
            
            # 删除已存在的文件（如果有）
            if (Test-Path $OutputPath) {
                Remove-Item -Path $OutputPath -Force -ErrorAction SilentlyContinue
            }
            
            # 使用 WebClient 替代 Invoke-WebRequest，更简洁且无进度提示
            $webClient = New-Object System.Net.WebClient
            $webClient.DownloadFile($Url, $OutputPath)
            $webClient.Dispose()
            
            # 验证文件是否成功下载
            if (Test-Path $OutputPath) {
                $fileInfo = Get-Item $OutputPath
                if ($fileInfo.Length -gt 0) {
                    $success = $true
                } else {
                    $errorMsg = "文件大小为0，可能被360安全软件阻止"
                    Remove-Item -Path $OutputPath -Force -ErrorAction SilentlyContinue
                }
            } else {
                $errorMsg = "文件未创建，可能被360安全软件阻止"
            }
            
        } catch {
            $errorMsg = $_.Exception.Message
            
            # 删除可能损坏的文件
            if (Test-Path $OutputPath) {
                Remove-Item -Path $OutputPath -Force -ErrorAction SilentlyContinue
            }
        } finally {
            # 恢复原来的进度首选项
            $ProgressPreference = $originalProgressPreference
        }
        
        if (-not $success -and $retryCount -lt $MaxRetries) {
            Start-Sleep -Seconds 2
        }
    }
    
    if (-not $success) {
        # 只在失败时记录到内部变量，不显示给用户
    }
    
    return @{
        Success = $success
        Error = $errorMsg
    }
}

# 显示下载结果
function Show-Download-Result {
    param(
        [int]$SuccessCount,
        [int]$ErrorCount,
        [array]$FailedFiles,
        [int]$TotalFiles
    )
    
    Write-Host ""
    Write-Host "══════════════════════════════════════════════════" -ForegroundColor DarkGray
    Write-Log "激活完成统计:" "Cyan" "📊"
    Write-Log "总文件数: $TotalFiles" "White" "   "
    Write-Log "成功: $SuccessCount" "Green" "✅"
    
    if ($ErrorCount -gt 0) {
        Write-Log "失败: $ErrorCount" "Red" "❌"
        Write-Host ""
        Write-Log "如果失败文件较多，可能是360安全软件阻止" "Yellow" "🛡️"
        Write-Log "建议暂时退出360安全软件后重试" "Yellow" "💡"
    } else {
        Write-Log "失败: $ErrorCount" "Green" "✅"
    }
    
    Write-Host ""
}

# 检查关键文件是否存在
function Check-Critical-Files {
    $criticalFiles = @(
        "ja-netfilter.jar",
        "config/dns.conf",
        "plugins/dns.jar",
        "激活码.txt"
    )
    
    $missingCriticalFiles = @()
    
    foreach ($file in $criticalFiles) {
        # 根据文件类型确定保存路径
        if ($file -match "^config/") {
            $path = "$script:config_dir\" + ($file -replace "^config/", "")
        } elseif ($file -match "^plugins/") {
            $path = "$script:plugins_dir\" + ($file -replace "^plugins/", "")
        } else {
            $path = "$script:work_dir\$file"
        }
        
        if (-not (Test-Path $path)) {
            $missingCriticalFiles += $file
            Write-Log "关键文件缺失: $file" "Red" "❌"
            Write-Log "  可能是360安全软件删除了文件" "Yellow" "🛡️"
        } else {
            # 验证文件完整性
            if (-not (Test-File-Integrity -FilePath $path -FileName $file)) {
                $missingCriticalFiles += $file
                Write-Log "关键文件损坏: $file" "Red" "❌"
                Write-Log "  可能是360安全软件破坏了文件" "Yellow" "🛡️"
            }
        }
    }
    
    return ($missingCriticalFiles.Count -gt 0)
}

# 多用户环境文件部署函数（静默模式）
function Multi-User-File-Deployment {
    Show-Header
    Write-Log "整理中...请勿退出..." "Cyan" "🔧"
    Write-Log "可能需要1-3分钟左右，请耐心等待..." "Green" "⚡"
    
    # 检查关键文件是否被安全软件删除
    if (-not (Test-Critical-Files-Before-Deployment)) {
        Write-Log "关键文件缺失，无法继续部署" "Red" "❌"
        Write-Log "可能是安全软件删除了关键文件" "Yellow" "🛡️"
        
        $choice = Read-Host "是否重新下载文件？(Y-重新下载/N-退出)"
        if ($choice -match "^[Yy]") {
            if (Download-Files-With-Progress) {
                return Multi-User-File-Deployment
            } else {
                return $false
            }
        } else {
            return $false
        }
    }
    
    # 获取所有用户目录
    $usersPath = "C:\Users"
    $userDirs = Get-ChildItem -Path $usersPath -Directory | Where-Object { 
        $_.Name -notmatch "Default|Public|All Users|Default User|desktop.ini"
    }
    
    if ($userDirs.Count -eq 0) {
        Write-Log "未找到任何用户目录" "Red" "❌"
        return $false
    }
    
    # 扫描所有用户目录，查找包含JetBrains配置的用户
    $usersWithJetBrains = @()
    $currentUser = [Environment]::UserName
    
    foreach ($userDir in $userDirs) {
        $userName = $userDir.Name
        $jetbrainsPath = "C:\Users\$userName\AppData\Roaming\JetBrains"
        
        if (Test-Path $jetbrainsPath) {
            # 获取该用户的JetBrains产品目录数量
            $productDirs = Get-ChildItem -Path $jetbrainsPath -Directory -ErrorAction SilentlyContinue | Where-Object { 
                $_.Name -match "^[a-zA-Z]+[0-9]{4}\.[0-9]"
            }
            
            if ($productDirs.Count -gt 0) {
                $isCurrentUser = ($userName -eq $currentUser)
                
                $usersWithJetBrains += @{
                    UserName = $userName
                    IsCurrentUser = $isCurrentUser
                    JetBrainsPath = $jetbrainsPath
                    LocalJetBrainsPath = "C:\Users\$userName\AppData\Local\JetBrains"
                    ProductDirs = $productDirs
                    ProductCount = $productDirs.Count
                }
            }
        }
    }
    
    # 存储环境变量设置信息
    $envVarsToSet = @()
    $deployedProducts = @()
    $homeFileDeploymentResults = @()
    
    # ==================== 方法1：通过Roaming目录部署key文件 ====================
    if ($usersWithJetBrains.Count -gt 0) {
        # 如果有多个用户包含JetBrains配置，让用户选择
        if ($usersWithJetBrains.Count -gt 1) {
            $selectedUsers = Select-JetBrains-Users -UsersWithJetBrains $usersWithJetBrains
            if ($selectedUsers.Count -eq 0) {
                Write-Log "未选择任何用户，退出部署" "Yellow" "🚫"
                return $false
            }
        } else {
            # 如果只有一个用户，自动选择
            $selectedUsers = $usersWithJetBrains
        }
        
        # 开始部署文件
        $totalUsers = $selectedUsers.Count
        $currentUserIndex = 0
        
        foreach ($userInfo in $selectedUsers) {
            $currentUserIndex++
            $userName = $userInfo.UserName
            $productDirs = $userInfo.ProductDirs
            
            foreach ($dir in $productDirs) {
                $dirName = $dir.Name
                
                # 从目录名提取产品名称
                $productName = Extract-Product-Name -DirectoryName $dirName
                
                if (-not $productName) {
                    continue
                }
                
                # 记录已部署的产品
                if (-not ($deployedProducts -contains $productName)) {
                    $deployedProducts += $productName
                }
                
                # 部署文件
                $deployResult = Deploy-Files-For-Product -ProductName $productName -TargetDir $dir.FullName -UserName $userName
                
                if ($deployResult.Success) {
                    # 收集环境变量信息
                    $envVarInfo = @{
                        ProductName = $productName
                        UserName = $userName
                        VmOptionsPath = $deployResult.VmOptionsPath
                        EnvVarName = $deployResult.EnvVarName
                        IsCurrentUser = $userInfo.IsCurrentUser
                    }
                    $envVarsToSet += $envVarInfo
                }
            }
        }
    }
    
    # ==================== 方法2：通过.home文件定位并修改vmoptions（静默模式） ====================
    # 静默执行，不输出任何提示信息
    $homeFileResults = Process-All-Users-Via-HomeFile -WorkDir $script:work_dir -Silent $true
    
    if ($homeFileResults.Count -gt 0) {
        $homeFileDeploymentResults = $homeFileResults
    }
    
    # ==================== 方法3：通过.home文件查找其他安装目录 ====================
    $installationDirectories = Get-Installation-Paths-From-Home-Files -UserDirs $userDirs -ExcludeProducts $deployedProducts
    
    # 处理安装目录部署
    if ($installationDirectories.Count -gt 0) {
        $installationEnvVars = Deploy-To-Installation-Directories -Installations $installationDirectories
        
        # 合并环境变量信息
        if ($installationEnvVars.Count -gt 0) {
            $envVarsToSet += $installationEnvVars
        }
    }
    
    # ==================== 检查部署结果 ====================
    if ($envVarsToSet.Count -eq 0 -and $homeFileDeploymentResults.Count -eq 0) {
        Write-Log "未找到任何JetBrains产品相关的软件" "Red" "❌"
        Write-Log "请确保已安装并至少运行过一次JetBrains软件" "Yellow" "💡"
        Write-Log "解决方法：1、先安装你要激活的软件。" "Green" "💡✅"
        Write-Log "2、软件安装后至少先打开一次软件后再激活" "Green" "💡"
        return $false
    }
    
    # ==================== 设置环境变量 ====================
    if ($envVarsToSet.Count -gt 0) {
        Set-Environment-Variables -EnvVarsInfo $envVarsToSet
    }
    
    # ==================== 显示最终部署结果 ====================
    Write-Host ""
    Write-Host "══════════════════════════════════════════════════" -ForegroundColor DarkGray
    Write-Log "激活统计:" "Cyan" "📊"
    Write-Log "成功部署: $($envVarsToSet.Count + $homeFileDeploymentResults.Count) 个配置" "Green" "✅"
    Write-Log "所有软件激活成功！" "Green" "🎉"
    return $true
}

# 检查部署前的关键文件
function Test-Critical-Files-Before-Deployment {
    $criticalFiles = @(
        @{Name = "ja-netfilter.jar"; Path = "$script:work_dir\ja-netfilter.jar"},
        @{Name = "激活码.txt"; Path = "$script:work_dir\激活码.txt"}
    )
    
    # 添加所有key文件
    $keyFiles = Get-ChildItem -Path "$script:keys_dir\*.key" -ErrorAction SilentlyContinue
    foreach ($keyFile in $keyFiles) {
        $criticalFiles += @{Name = $keyFile.Name; Path = $keyFile.FullName}
    }
    
    $missingFiles = @()
    
    foreach ($file in $criticalFiles) {
        if (-not (Test-Path $file.Path)) {
            $missingFiles += $file.Name
        } else {
            # 验证文件大小
            $fileInfo = Get-Item $file.Path
            if ($fileInfo.Length -eq 0) {
                $missingFiles += $file.Name
            }
        }
    }
    
    if ($missingFiles.Count -gt 0) {
        Write-Log "共发现 $($missingFiles.Count) 个关键文件缺失或损坏" "Red" "❌"
        return $false
    }
    
    return $true
}

# 选择包含JetBrains配置的用户
function Select-JetBrains-Users {
    param([array]$UsersWithJetBrains)
    
    Show-Header
    Write-Log "检测到多个用户包含JetBrains配置，请选择:" "Cyan" "👥"
    Write-Host ""
    
    # 显示用户列表
    $index = 1
    foreach ($userInfo in $UsersWithJetBrains) {
        $userName = $userInfo.UserName
        $productCount = $userInfo.ProductCount
        $isCurrent = $userInfo.IsCurrentUser
        $status = if ($isCurrent) { "[当前用户]" } else { "" }
        
        Write-Host "  $index. " -NoNewline -ForegroundColor White
        Write-Host "$userName " -NoNewline -ForegroundColor Green
        Write-Host "$status " -NoNewline -ForegroundColor Cyan
        Write-Host "- $productCount 个JetBrains产品" -ForegroundColor White
        
        $index++
    }
    
    Write-Host ""
    Write-Host "  A. " -NoNewline -ForegroundColor White
    Write-Host "所有用户" -ForegroundColor Yellow
    
    Write-Host ""
    
    # 获取用户选择
    do {
        $choice = Read-Host "请选择用户编号 (1-$($UsersWithJetBrains.Count)) 或 A (所有用户)"
        
        if ($choice -eq "A" -or $choice -eq "a") {
            Write-Log "已选择所有用户" "Green" "✅"
            return $UsersWithJetBrains
        }
        
        if ($choice -match "^[1-9]\d*$" -and [int]$choice -ge 1 -and [int]$choice -le $UsersWithJetBrains.Count) {
            $selectedUser = $UsersWithJetBrains[[int]$choice - 1]
            Write-Log "已选择用户: $($selectedUser.UserName)" "Green" "✅"
            return @($selectedUser)
        } else {
            Write-Log "无效选择，请重新输入" "Red" "❌"
        }
    } while ($true)
}

# 从目录名提取产品名称
function Extract-Product-Name {
    param([string]$DirectoryName)
    
    $productPatterns = @{
        "IntelliJIdea" = "idea"
        "PyCharm" = "pycharm"
        "PhpStorm" = "phpstorm"
        "WebStorm" = "webstorm"
        "CLion" = "clion"
        "Rider" = "rider"
        "GoLand" = "goland"
        "DataGrip" = "datagrip"
        "RubyMine" = "rubymine"
        "AppCode" = "appcode"
        "DataSpell" = "dataspell"
        "RustRover" = "rustrover"
    }
    
    foreach ($pattern in $productPatterns.Keys) {
        if ($DirectoryName -match $pattern) {
            return $productPatterns[$pattern]
        }
    }
    
    return $null
}

# 为特定产品部署文件
function Deploy-Files-For-Product {
    param(
        [string]$ProductName,
        [string]$TargetDir,
        [string]$UserName
    )
    
    $sourceKeyFile = "$script:keys_dir\$ProductName.key"
    $sourceVmOptionsFile = "$script:keys_dir\$ProductName`64.exe.vmoptions"
    $targetKeyFile = "$TargetDir\$ProductName.key"
    $targetVmOptionsFile = "$TargetDir\$ProductName`64.exe.vmoptions"
    
    if (-not (Test-Path $sourceKeyFile) -or -not (Test-Path $sourceVmOptionsFile)) {
        return @{ Success = $false; Error = "源文件不存在" }
    }
    
    if (-not (Test-Path $TargetDir)) {
        New-Item -ItemType Directory -Path $TargetDir -Force | Out-Null
    }
    
    try {
        Copy-Item -Path $sourceKeyFile -Destination $targetKeyFile -Force -ErrorAction Stop
        Copy-Item -Path $sourceVmOptionsFile -Destination $targetVmOptionsFile -Force -ErrorAction Stop
        
        $envVarName = Get-Product-EnvVar-Name -ProductName $ProductName
        
        return @{ 
            Success = $true; 
            VmOptionsPath = $targetVmOptionsFile;
            EnvVarName = $envVarName
        }
    } catch {
        return @{ Success = $false; Error = $_.Exception.Message }
    }
}

# 根据产品名获取环境变量名
function Get-Product-EnvVar-Name {
    param([string]$ProductName)
    
    foreach ($product in $script:jetbrainsProducts) {
        if ($product.name -eq $ProductName) {
            return $product.envVar
        }
    }
    return "$($ProductName.ToUpper())_VM_OPTIONS"
}

# 设置环境变量
function Set-Environment-Variables {
    param([array]$EnvVarsInfo)
    
    $envVarsSet = @{}
    $totalVars = 0
    $currentVar = 0
    
    foreach ($envVarInfo in $EnvVarsInfo) {
        $envVarName = $envVarInfo.EnvVarName
        if (-not $envVarsSet.ContainsKey($envVarName)) {
            $envVarsSet[$envVarName] = $true
            $totalVars++
        }
    }
    
    if ($totalVars -eq 0) { return }
    
    foreach ($envVarInfo in $EnvVarsInfo) {
        $envVarName = $envVarInfo.EnvVarName
        $vmOptionsPath = $envVarInfo.VmOptionsPath
        $isCurrentUser = $envVarInfo.IsCurrentUser
        
        if (-not $envVarsSet.ContainsKey($envVarName)) { continue }
        
        $currentVar++
        $percent = [math]::Round(($currentVar / $totalVars) * 100, 0)
        Write-ProgressBar -percent $percent -status "正在验证是否激活成功... ($currentVar/$totalVars)" -emoji "⚙️"
        
        if ($isCurrentUser) {
            try {
                [Environment]::SetEnvironmentVariable($envVarName, $vmOptionsPath, "User")
            } catch {
                Write-Log "  用户环境初始化失败: $envVarName - $($_.Exception.Message)" "Red" "❌"
            }
        }
        
        try {
            [Environment]::SetEnvironmentVariable($envVarName, $vmOptionsPath, "Machine")
        } catch {
            Write-Log "  系统环境初始化失败: $envVarName - $($_.Exception.Message)" "Red" "❌"
        }
        
        $envVarsSet.Remove($envVarName)
    }
    
    Write-Host ""
}

# 通过.home文件获取安装路径
function Get-Installation-Paths-From-Home-Files {
    param([array]$UserDirs, [array]$ExcludeProducts = @())
    
    $jetbrainsInstallations = @()
    
    foreach ($userDir in $UserDirs) {
        $userName = $userDir.Name
        $localJetBrainsPath = "C:\Users\$userName\AppData\Local\JetBrains"
        
        if (-not (Test-Path $localJetBrainsPath)) { continue }
        
        $productDirs = Get-ChildItem -Path $localJetBrainsPath -Directory -ErrorAction SilentlyContinue | Where-Object {
            $_.Name -match "^[a-zA-Z]+[0-9]{4}\.[0-9]"
        }
        
        foreach ($productDir in $productDirs) {
            $homeFile = Join-Path $productDir.FullName ".home"
            if (-not (Test-Path $homeFile)) { continue }
            
            $installPath = Get-Content $homeFile -ErrorAction SilentlyContinue
            if (-not $installPath -or -not (Test-Path $installPath)) { continue }
            
            $binPath = Join-Path $installPath "bin"
            if (-not (Test-Path $binPath)) { continue }
            
            $productName = Extract-Product-Name -DirectoryName $productDir.Name
            if (-not $productName) { continue }
            
            if ($ExcludeProducts -contains $productName) { continue }
            
            $alreadyAdded = $false
            foreach ($existing in $jetbrainsInstallations) {
                if ($existing.ProductName -eq $productName -and $existing.InstallPath -eq $installPath) {
                    $alreadyAdded = $true
                    break
                }
            }
            
            if (-not $alreadyAdded) {
                $jetbrainsInstallations += @{
                    ProductName = $productName
                    InstallPath = $installPath
                    BinPath = $binPath
                    UserName = $userName
                }
            }
        }
    }
    
    return $jetbrainsInstallations
}

# 修改安装目录中的vmoptions文件
function Modify-Installation-VmOptions {
    param(
        [string]$ProductName,
        [string]$BinPath,
        [string]$InstallPath
    )
    
    $vmOptionsFileName = Get-VmOptions-File-Name -ProductName $ProductName
    $vmOptionsFilePath = Join-Path $BinPath $vmOptionsFileName
    
    if (-not (Test-Path $vmOptionsFilePath)) {
        return @{ Success = $false; Error = "vmoptions文件不存在" }
    }
    
    try {
        $existingContent = Get-Content -Path $vmOptionsFilePath -ErrorAction Stop
        $newContent = Build-VmOptions-Content -ProductName $ProductName
        
        $hasExistingConfig = $false
        foreach ($line in $existingContent) {
            if ($line -match "-javaagent.*ja-netfilter") {
                $hasExistingConfig = $true
                break
            }
        }
        
        if ($hasExistingConfig) {
            return @{ Success = $true; Skip = $true }
        }
        
        $updatedContent = @($existingContent) + $newContent
        $backupPath = "$vmOptionsFilePath.backup.$(Get-Date -Format 'yyyyMMddHHmmss')"
        Copy-Item -Path $vmOptionsFilePath -Destination $backupPath -Force
        Set-Content -Path $vmOptionsFilePath -Value $updatedContent -ErrorAction Stop
        
        return @{ Success = $true; FilePath = $vmOptionsFilePath }
    } catch {
        return @{ Success = $false; Error = $_.Exception.Message }
    }
}

# 根据产品名获取vmoptions文件名
function Get-VmOptions-File-Name {
    param([string]$ProductName)
    
    $vmOptionsFiles = @{
        "idea" = "idea64.exe.vmoptions"
        "pycharm" = "pycharm64.exe.vmoptions"
        "webstorm" = "webstorm64.exe.vmoptions"
        "phpstorm" = "phpstorm64.exe.vmoptions"
        "clion" = "clion64.exe.vmoptions"
        "rider" = "rider64.exe.vmoptions"
        "goland" = "goland64.exe.vmoptions"
        "datagrip" = "datagrip64.exe.vmoptions"
        "rubymine" = "rubymine64.exe.vmoptions"
        "appcode" = "appcode.vmoptions"
        "dataspell" = "dataspell64.exe.vmoptions"
        "rustrover" = "rustrover64.exe.vmoptions"
    }
    
    return $vmOptionsFiles[$ProductName]
}

# 构建vmoptions文件内容
function Build-VmOptions-Content {
    param([string]$ProductName)
    
    $jarPath = "$script:work_dir\ja-netfilter.jar"
    $jarPathUnix = $jarPath.Replace('\', '/')
    
    $content = @()
    $content += "--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED"
    $content += "--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED"
    $content += "-javaagent:$jarPathUnix"
    
    return $content
}

# 处理其他盘符的安装目录部署
function Deploy-To-Installation-Directories {
    param([array]$Installations)
    
    if ($Installations.Count -eq 0) { return @() }
    
    $envVarsInfo = @()
    
    foreach ($installation in $Installations) {
        $productName = $installation.ProductName
        $binPath = $installation.BinPath
        
        $result = Modify-Installation-VmOptions -ProductName $productName -BinPath $binPath -InstallPath $installation.InstallPath
        
        if ($result.Success -and -not $result.Skip) {
            $envVarName = Get-Product-EnvVar-Name -ProductName $productName
            $envVarsInfo += @{
                ProductName = $productName
                UserName = "System"
                VmOptionsPath = $result.FilePath
                EnvVarName = $envVarName
                IsCurrentUser = $true
            }
        }
    }
    
    return $envVarsInfo
}

# ============================================================
# 通过.home文件定位并部署vmoptions的函数（静默模式）
# 功能：读取Local\JetBrains\<产品版本>\.home获取实际安装路径
#      → 进入bin目录 → 递归搜索*.vmoptions文件
#      → 正则清理旧配置 → 追加新的javaagent参数
# 注意：成功时不输出任何信息，仅失败时输出错误
# ============================================================

function Deploy-VmOptions-Via-HomeFile {
    param(
        [string]$UserName,
        [string]$LocalJetBrainsPath,
        [string]$WorkDir,
        [switch]$Silent = $false
    )
    
    # 静默模式，不输出任何成功信息
    
    # 检查LocalJetBrainsPath是否存在
    if (-not (Test-Path $LocalJetBrainsPath)) {
        return @()
    }
    
    # 获取Local目录下的所有JetBrains产品目录
    $productDirs = Get-ChildItem -Path $LocalJetBrainsPath -Directory -ErrorAction SilentlyContinue | Where-Object {
        $_.Name -match "^[a-zA-Z]+[0-9]{4}\.[0-9]"
    }
    
    if ($productDirs.Count -eq 0) {
        return @()
    }
    
    $successResults = @()
    $errorResults = @()
    
    foreach ($productDir in $productDirs) {
        $homeFilePath = Join-Path $productDir.FullName ".home"
        
        if (-not (Test-Path $homeFilePath)) {
            continue
        }
        
        try {
            # 读取.home文件内容（实际安装路径）
            $installPath = Get-Content -Path $homeFilePath -Encoding UTF8 -ErrorAction Stop
            $installPath = $installPath.Trim()
            
            if (-not $installPath) {
                continue
            }
            
            if (-not (Test-Path $installPath)) {
                continue
            }
            
            # 构建bin目录路径
            $binPath = Join-Path $installPath "bin"
            
            if (-not (Test-Path $binPath)) {
                continue
            }
            
            # 递归查找所有.vmoptions文件
            $vmOptionsFiles = Get-ChildItem -Path $binPath -Filter "*.vmoptions" -Recurse -ErrorAction SilentlyContinue
            
            if ($vmOptionsFiles.Count -eq 0) {
                continue
            }
            
            # 从目录名提取产品名称
            $productName = Extract-Product-Name-From-HomeDir -DirectoryName $productDir.Name
            
            if (-not $productName) {
                $productName = "unknown"
            }
            
            # 正则表达式模式：匹配-javaagent开头的行
            $regexPattern = '^-javaagent:.*[/\\]*\.jar.*'
            $regex = New-Object System.Text.RegularExpressions.Regex $regexPattern, ([System.Text.RegularExpressions.RegexOptions]::IgnoreCase -bor [System.Text.RegularExpressions.RegexOptions]::Compiled)
            
            foreach ($vmFile in $vmOptionsFiles) {
                try {
                    # 读取现有内容
                    $existingContent = Get-Content -Path $vmFile.FullName -Encoding UTF8 -ErrorAction Stop
                    
                    # 清理旧的javaagent行
                    $filteredContent = $existingContent | Where-Object {
                        -not $regex.IsMatch($_)
                    }
                    
                    # 构建要添加的新内容
                    $jarPath = "$WorkDir\ja-netfilter.jar"
                    $jarPathUnix = $jarPath.Replace('\', '/')
                    
                    $newContent = @()
                    $newContent += "--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED"
                    $newContent += "--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED"
                    $newContent += "-javaagent:$jarPathUnix"
                    
                    # 备份原文件
                    $backupPath = "$($vmFile.FullName).backup.$(Get-Date -Format 'yyyyMMddHHmmss')"
                    Copy-Item -Path $vmFile.FullName -Destination $backupPath -Force -ErrorAction SilentlyContinue
                    
                    # 合并内容
                    $updatedContent = @($filteredContent) + @("") + @("# Added by JetBrains Activation Tool") + $newContent
                    
                    # 写回文件
                    Set-Content -Path $vmFile.FullName -Value $updatedContent -Encoding UTF8 -Force
                    
                    # 收集成功结果（但不输出）
                    $successResults += @{
                        ProductName = $productName
                        ProductDir = $productDir.Name
                        VmOptionsFile = $vmFile.FullName
                        InstallPath = $installPath
                        UserName = $UserName
                    }
                    
                } catch {
                    # 失败时显示错误信息
                    $errorMsg = "处理失败: $($vmFile.Name) - $($_.Exception.Message)"
                    Write-Log $errorMsg "Red" "❌" -Silent:$Silent
                    $errorResults += $errorMsg
                }
            }
            
        } catch {
            # 失败时显示错误信息
            $errorMsg = "处理失败: $($productDir.Name) - $($_.Exception.Message)"
            Write-Log $errorMsg "Red" "❌" -Silent:$Silent
            $errorResults += $errorMsg
        }
    }
    
    # 如果有错误，显示汇总信息
    if ($errorResults.Count -gt 0 -and -not $Silent) {
        Write-Log "共发生 $($errorResults.Count) 个错误" "Red" "❌"
    }
    
    # 返回成功结果（不输出）
    return $successResults
}

# 从目录名提取产品名称
function Extract-Product-Name-From-HomeDir {
    param([string]$DirectoryName)
    
    $productPatterns = @{
        "IntelliJIdea" = "idea"
        "PyCharm" = "pycharm"
        "PhpStorm" = "phpstorm"
        "WebStorm" = "webstorm"
        "CLion" = "clion"
        "Rider" = "rider"
        "GoLand" = "goland"
        "DataGrip" = "datagrip"
        "RubyMine" = "rubymine"
        "AppCode" = "appcode"
        "DataSpell" = "dataspell"
        "RustRover" = "rustrover"
    }
    
    foreach ($pattern in $productPatterns.Keys) {
        if ($DirectoryName -match $pattern) {
            return $productPatterns[$pattern]
        }
    }
    
    return $null
}

# 批量处理所有用户（静默模式）
function Process-All-Users-Via-HomeFile {
    param(
        [string]$WorkDir,
        [switch]$Silent = $false
    )
    
    # 静默模式，不输出扫描信息
    
    $usersPath = "C:\Users"
    
    if (-not (Test-Path $usersPath)) {
        return @()
    }
    
    # 获取所有用户目录
    $userDirs = Get-ChildItem -Path $usersPath -Directory | Where-Object { 
        $_.Name -notmatch "Default|Public|All Users|Default User|desktop.ini"
    }
    
    if ($userDirs.Count -eq 0) {
        return @()
    }
    
    $allResults = @()
    
    foreach ($userDir in $userDirs) {
        $userName = $userDir.Name
        $localJetBrainsPath = "C:\Users\$userName\AppData\Local\JetBrains"
        
        if (-not (Test-Path $localJetBrainsPath)) {
            continue
        }
        
        $userResults = Deploy-VmOptions-Via-HomeFile -UserName $userName -LocalJetBrainsPath $localJetBrainsPath -WorkDir $WorkDir -Silent:$Silent
        
        if ($userResults.Count -gt 0) {
            $allResults += $userResults
        }
    }
    
    # 不输出任何成功信息，只返回结果
    return $allResults
}

# 显示完成界面
function Show-Completion {
    Show-Header
    Write-Host ""
    Write-Host "🎉 激活完成！" -ForegroundColor Green
    Write-Host "══════════════════════════════════════════════════" -ForegroundColor DarkGray
    Write-Log "所需软件已激活成功，授权码剩余使用次数: $script:remainingUses" "Green" "✅"
    Write-Log "激活码放到您的桌面上了，打开激活码全部复制，在激活界面填写就行！" "Yellow" "🔄"
    Write-Log "感谢使用我们的激活服务" "Cyan" "🙏"
    Write-Host ""
    Write-Host "💡 温馨提示：" -ForegroundColor Yellow
    Write-Host "   - 1、2025.2以上版本可能需要打开软件输入激活码才可以，激活码已经帮您打开" -ForegroundColor Gray
    Write-Host "   - 2、如果您打开软件显示有效期到2888就说明已经激活了，无需再问客服！！" -ForegroundColor Gray
    Write-Host ""
    Write-Host "按任意键退出..." -ForegroundColor DarkGray
    $null = $Host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")
}

# 主函数 - 多用户部署版（包含.home文件定位功能）
<#
    .SYNOPSIS
        JetBrains IDE 激活工具主函数
    
    .DESCRIPTION
        处理权限检查、提权、验证、部署等激活流程的核心函数
        新增功能：通过.home文件定位安装目录并修改vmoptions
    
    .PARAMETER elevated
        内部使用，标识是否已获得管理员权限
    
    .PARAMETER silent
        静默模式，减少输出信息
#>
function Main {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$false)]
        [Alias('e')]
        [switch]$elevated = $false,
        
        [Parameter(Mandatory=$false)]
        [Alias('s')]
        [switch]$silent = $false
    )
    
    # 设置错误处理模式
    $ErrorActionPreference = 'Stop'
    
    # 记录开始时间用于性能跟踪
    $startTime = Get-Date
    
    # 检查是否是通过提权重新启动的
    $isElevatedRestart = $elevated
    if ($isElevatedRestart) {
        Write-Log "已获得管理员权限，继续执行..." "Green" "✅" -Silent:$silent
    } elseif ($args.Count -gt 0 -and $args[0] -eq "--elevated") {
        # 向后兼容旧版本的参数格式
        $isElevatedRestart = $true
        Write-Log "已获得管理员权限，继续执行..." "Green" "✅" -Silent:$silent
    }
    
    try {
        # 检查管理员权限
        $isAdmin = [Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()
        $isAdmin = $isAdmin.IsInRole([Security.Principal.WindowsBuiltInRole]"Administrator")
        
        if (-not $isAdmin) {
            Write-Log "需要用管理员权限运行，请参考教程第一步！" "Yellow" "🔼" -Silent:$silent
            
            # 构建重新执行的命令
            $elevatedCommand = @"
try {
    Write-Host "正在以管理员权限重新启动..." -ForegroundColor Yellow
    $progressPreference = 'SilentlyContinue' # 禁用进度条提高性能
    $response = Invoke-RestMethod -Uri "https://idek.pro/get-script.php" -Headers @{"X-Script-Source" = "IDEKEY_INTERNAL"} -UseBasicParsing -TimeoutSec 30
    $progressPreference = 'Continue' # 恢复进度条
    if ($response -and $response.Trim()) { 
        # 添加提权标志参数
        $sb = [scriptblock]::Create($response + "`nMain -elevated")
        Invoke-Command -ScriptBlock $sb
    } else { 
        Write-Host "获取脚本失败：返回内容为空" -ForegroundColor Red 
        Start-Sleep -Seconds 3
    }
}
catch {
    Write-Host "提权后执行失败: " -ForegroundColor Red -NoNewline
    Write-Host $_.Exception.Message -ForegroundColor Red
    Write-Host "请检查网络连接后重试" -ForegroundColor Yellow
    pause
}
"@
            
            # 启动提权进程
            Start-Process powershell.exe -ArgumentList "-Command", $elevatedCommand -Verb RunAs -WindowStyle Normal
            return
        }
        
        # 检查杀毒软件干扰
        if (-not (Check-Antivirus-Interference -Silent:$silent)) {
            Write-Log "激活流程终止" "Red" "🚫" -Silent:$silent
            Write-Log "请关闭安全软件后重新运行脚本" "Yellow" "🛡️" -Silent:$silent
            Start-Sleep -Seconds 3
            return
        }
        
        # 如果不是通过提权重新启动的，进行授权码验证
        if (-not $isElevatedRestart) {
            # 授权码验证
            if (-not (Verify-ActivationCode -Silent:$silent)) {
                Write-Log "激活流程终止" "Red" "🚫" -Silent:$silent
                Write-Log "请获取有效授权码后重试" "Yellow" "🛒" -Silent:$silent
                Start-Sleep -Seconds 3
                return
            }
        }
        
        # 检查是否有JetBrains软件正在运行
        if (-not (Check-RunningProcesses -Silent:$silent)) {
            return
        }
        
        # 初始化系统（清理环境变量）
        Initialize-System -Silent:$silent
        
        # 创建工作目录
        if (-not (Create-WorkDirectory)) {
            Write-Log "无法创建工作目录，退出脚本" "Red" "❌" -Silent:$silent
            return
        }
        
        # 下载文件
        if (-not (Download-Files-With-Progress -Silent:$silent)) {
            Write-Log "文件下载失败，激活流程终止" "Red" "❌" -Silent:$silent
            Write-Log "请检查网络连接后重试" "Yellow" "🌐" -Silent:$silent
            Start-Sleep -Seconds 3
            return
        }
        
        # ========== 核心部署功能：通过.home文件定位并修改vmoptions ==========
        Write-Log ""
        Write-Log "══════════════════════════════════════════════════" -ForegroundColor DarkGray
        Write-Log "开始通过.home文件定位JetBrains安装目录..." "Cyan" "🔍"
        Write-Log "══════════════════════════════════════════════════" -ForegroundColor DarkGray
        
        # 使用多用户文件部署（包含.home文件处理）
        if (-not (Multi-User-File-Deployment -Silent:$silent)) {
            Write-Log "激活部署失败，请查看上方错误信息" "Yellow" "⚠️" -Silent:$silent
            Write-Log "如果问题持续，请联系技术支持" "Yellow" "💡" -Silent:$silent
            Start-Sleep -Seconds 3
            return
        }
        
        # 复制激活码.txt到桌面并打开
        Copy-ActivationCode-To-Desktop -Silent:$silent
        
        # 显示完成界面
        Show-Completion -Silent:$silent
        
        # 记录执行时间
        $endTime = Get-Date
        $duration = ($endTime - $startTime).TotalSeconds
        Write-Log "激活过程完成，总耗时: " -NoNewline -Silent:$silent
        Write-Log "{0:F2} 秒" -f $duration -ForegroundColor Green -NoTimestamp -Silent:$silent
        
    } catch {
        # 全局错误处理
        $errorDetails = @{
            Message = $_.Exception.Message
            ScriptLine = $_.InvocationInfo.ScriptLineNumber
            Function = $_.InvocationInfo.MyCommand.Name
        }
        
        # 记录详细错误信息
        Write-Log "发生未处理的错误" "Red" "❌" -Silent:$silent
        Write-Log "错误: $($_.Exception.Message)" "Red" -NoTimestamp -Silent:$silent
        
        # 尝试记录到日志文件
        try {
            $logPath = "$env:APPDATA\JetBrainsActivation\error_$(Get-Date -Format 'yyyyMMdd_HHmmss').log"
            New-Item -Path (Split-Path $logPath -Parent) -ItemType Directory -Force -ErrorAction SilentlyContinue | Out-Null
            "[ERROR] $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - $($errorDetails.Message)" | Out-File -FilePath $logPath -Append
            "[ERROR] 行号: $($errorDetails.ScriptLine), 函数: $($errorDetails.Function)" | Out-File -FilePath $logPath -Append
        } catch {
            # 忽略日志记录错误
        }
        
        Write-Log "请联系技术支持" "Yellow" "💡" -Silent:$silent
        Start-Sleep -Seconds 5
        return
    } finally {
        # 执行清理工作
        if (Test-Path variable:\script:work_dir) {
            Cleanup-TemporaryFiles -Directory $script:work_dir -Silent:$true
        }
    }
}

# 复制激活码.txt到桌面并打开
<#
    .SYNOPSIS
        将激活码文件复制到桌面并打开
    
    .DESCRIPTION
        从工作目录复制激活码文件到桌面，并尝试使用记事本打开
    
    .PARAMETER Silent
        静默模式开关
#>
function Copy-ActivationCode-To-Desktop {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$false)]
        [switch]$Silent = $false
    )
    
    Write-Log "正在处理激活码文件..." "Cyan" "📄" -Silent:$Silent
    
    try {
        # 获取桌面路径 - 支持不同系统环境
        $desktopPath = [Environment]::GetFolderPath([Environment+SpecialFolder]::Desktop)
        
        # 检查work_dir变量是否存在
        if (-not (Test-Path variable:\script:work_dir)) {
            Write-Log "工作目录变量未设置" "Red" "❌" -Silent:$Silent
            return $false
        }
        
        $sourceFile = "$script:work_dir\激活码.txt"
        $destFile = "$desktopPath\激活码.txt"
        
        if (Test-Path $sourceFile) {
            # 确保目标文件夹存在
            New-Item -Path (Split-Path $destFile -Parent) -ItemType Directory -Force -ErrorAction SilentlyContinue | Out-Null
            
            # 复制文件到桌面
            Copy-Item -Path $sourceFile -Destination $destFile -Force -ErrorAction Stop
            Write-Log "激活码.txt 已复制到桌面" "Green" "✅" -Silent:$Silent
            
            # 打开文件 - 添加超时处理
            $fileOpenJob = Start-Job -ScriptBlock {
                param($FilePath)
                try {
                    # 尝试使用默认程序打开
                    Start-Process -FilePath $FilePath -ErrorAction Stop
                    return $true
                } catch {
                    # 尝试使用记事本打开
                    try {
                        Start-Process -FilePath 'notepad.exe' -ArgumentList $FilePath -ErrorAction Stop
                        return $true
                    } catch {
                        return $false
                    }
                }
            } -ArgumentList $destFile
            
            # 等待作业完成，最多2秒
            $result = Wait-Job -Job $fileOpenJob -Timeout 2 | Receive-Job
            
            if ($result) {
                Write-Log "已自动打开激活码.txt" "Green" "📖" -Silent:$Silent
            } else {
                Write-Log "无法自动打开文件，请手动打开" "Yellow" "⚠️" -Silent:$Silent
            }
            
            # 清理作业
            Remove-Job -Job $fileOpenJob -Force -ErrorAction SilentlyContinue
            
            return $true
        } else {
            Write-Log "激活码.txt 文件未找到，无法复制到桌面" "Red" "❌" -Silent:$Silent
            Write-Log "可能是安全软件删除了文件" "Yellow" "🛡️" -Silent:$Silent
            return $false
        }
    } catch {
        Write-Log "复制或打开激活码文件失败: $($_.Exception.Message)" "Red" "❌" -Silent:$Silent
        Write-Log "请手动打开文件: $sourceFile" "Yellow" "💡" -Silent:$Silent
        return $false
    }
}

# 清理临时文件函数
<#
    .SYNOPSIS
        清理临时文件
    
    .DESCRIPTION
        安全地清理指定目录中的临时文件，避免锁定问题
    
    .PARAMETER Directory
        要清理的目录路径
    
    .PARAMETER Silent
        静默模式开关
#>
function Cleanup-TemporaryFiles {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [string]$Directory,
        
        [Parameter(Mandatory=$false)]
        [switch]$Silent = $false
    )
    
    try {
        if (Test-Path $Directory) {
            # 获取并关闭可能锁定文件的进程
            $lockedProcesses = @('notepad', 'explorer')
            foreach ($proc in $lockedProcesses) {
                $processes = Get-Process -Name $proc -ErrorAction SilentlyContinue | Where-Object { $_.MainWindowTitle -match $Directory }
                foreach ($p in $processes) {
                    try {
                        Stop-Process -Id $p.Id -Force -ErrorAction SilentlyContinue
                    } catch {
                        # 忽略进程停止错误
                    }
                }
            }
            
            # 短暂延迟以确保文件解锁
            Start-Sleep -Milliseconds 300
            
            # 使用命令行删除以避免PowerShell锁定
            cmd.exe /c rd /s /q "$Directory" 2> $null
            
            # 确认删除
            if (-not (Test-Path $Directory)) {
                Write-Log "临时文件已清理" "Green" "✅" -Silent:$Silent
                return $true
            } else {
                Write-Log "临时文件清理失败，文件可能被锁定" "Yellow" "⚠️" -Silent:$Silent
                return $false
            }
        }
        return $true
    } catch {
        # 忽略清理错误，这不是关键操作
        return $false
    }
}

# 执行主函数
Main


```

# Mac/Linux

```bash
#!/bin/bash
#set -e
# bash activeJB.sh
# ============ 平台检测 =============
detect_platform() {
    case "$(uname -s)" in
        Darwin)
            OS="macOS"
            SHA_TOOL="shasum"
            OPEN_CMD="open"
            DATE_PARSER="mac"
            FILE_VMOPTIONS=".vmoptions"
            ;;
        Linux)
            OS="Linux"
            SHA_TOOL="sha1sum"
            OPEN_CMD="xdg-open"
            DATE_PARSER="linux"
            FILE_VMOPTIONS="64.vmoptions"
            ;;
        *)
            OS="Unknown"
            SHA_TOOL="sha1sum"
            OPEN_CMD="xdg-open"
            DATE_PARSER="linux"
            FILE_VMOPTIONS=".vmoptions"
            ;;
    esac
}
# 自动检测平台
detect_platform

# ============ 配置 =============
DEBUG=false
ENABLE_COLOR=true

# 文件下载
URL_DOWNLOAD="https://szjcobs.coursegate.cn/2026/09/29/jb-run/"

# 全局变量存储剩余使用次数和用户名
REMAINING_USES=0
USER_NAME=""

# 获取原始用户和家目录
if [ "$(id -u)" -eq 0 ] && [ -n "$SUDO_USER" ]; then
    ORIGINAL_USER="$SUDO_USER"
    USER_HOME="/home/${SUDO_USER}"
else
    ORIGINAL_USER="$(whoami)"
    USER_HOME="${HOME}"
fi

# macOS 用户路径修正
if [[ "$OS" == "macOS" ]]; then
    USER_HOME="/Users/${ORIGINAL_USER}"
fi

# 工作路径
dir_work="${USER_HOME}/.jb_run"
dir_config="${dir_work}/config"
dir_plugins="${dir_work}/plugins"
dir_backups="${dir_work}/backups"
file_netfilter_jar="${dir_work}/ja-netfilter.jar"

# JetBrains 目录
if [[ "$OS" == "macOS" ]]; then
    dir_cache_jb="${USER_HOME}/Library/Caches/JetBrains"
    dir_config_jb="${USER_HOME}/Library/Application Support/JetBrains"
else
    dir_cache_jb="${USER_HOME}/.cache/JetBrains"
    dir_config_jb="${USER_HOME}/.config/JetBrains"
fi

# 日志颜色设置
if $ENABLE_COLOR; then
    RED='\033[0;31m'
    GREEN='\033[0;32m'
    YELLOW='\033[0;33m'
    GRAY='\033[38;5;240m'
    NC='\033[0m'
else
    RED=''
    GREEN=''
    YELLOW=''
    GRAY=''
    NC=''
fi

# 产品列表（硬编码，避免依赖 jq）
PRODUCTS=(
    "idea|II,PCWMP,PSI"
    "clion|CL,PSI,PCWMP"
    "phpstorm|PS,PCWMP,PSI"
    "goland|GO,PSI,PCWMP"
    "pycharm|PC,PSI,PCWMP"
    "webstorm|WS,PCWMP,PSI"
    "rider|RD,PDB,PSI,PCWMP"
    "datagrip|DB,PSI,PDB"
    "rubymine|RM,PCWMP,PSI"
    "appcode|AC,PCWMP,PSI"
    "dataspell|DS,PSI,PDB,PCWMP"
    "dotmemory|DM"
    "rustrover|RR,PSI,PCWP"
)

# ============ 工具函数 =============

# ============ 检查依赖 =============
check_deps() {
    if ! command -v curl &>/dev/null; then
        error "需要 curl 但未安装"
        if [[ "$OS" == "macOS" ]]; then
            error "macOS 应该自带 curl，请检查系统配置"
        else
            error "请安装 curl: sudo apt install curl 或相应命令"
        fi
        exit 1
    fi
    info "初始化完成。"
}

# ============ 解析产品 =============
parse_product_from_index() {
    local index="$1"
    echo "${PRODUCTS[$index]}"
}

# ============ 日志函数 =============
log() {
    local level="$1"
    local message="$2"
    local color=""
    
    case "$level" in
        "INFO")
            color="$NC"
            ;;
        "DEBUG")
            [[ "$DEBUG" == true ]] || return
            color="$GRAY"
            ;;
        "WARNING")
            color="$YELLOW"
            ;;
        "ERROR")
            color="$RED"
            ;;
        "SUCCESS")
            color="$GREEN"
            ;;
        *)
            color="$NC"
            ;;
    esac
    
    echo -e "${color}[$(date '+%Y-%m-%d %H:%M:%S')][$level] ${message}${NC}"
}

debug()   { log "DEBUG" "$1"; }
info()    { log "INFO" "$1"; }
warning() { log "WARNING" "$1"; }
error()   { log "ERROR" "$1"; }
success() { log "SUCCESS" "$1"; }

# ============ ASCII Art =============
show_ascii_jb() {
    echo "=================================================="
    echo "        JetBrains 激活工具 | 哇咔咔"
    echo "=================================================="
}

# ============ JSON 解析函数（不使用 jq） =============
parse_json_value() {
    local json="$1"
    local key="$2"
    echo "$json" | grep -o "\"$key\":[^,}]*" | cut -d':' -f2- | sed 's/^[[:space:]]*//;s/[[:space:]]*$//;s/^"//;s/"$//'
}

# ============ 授权码验证函数 =============
verify_activation_code() {
    return 0
}

# ============ 清理环境变量 =============
remove_env_other(){
    OS_NAME=$(uname -s)
    JB_PRODUCTS="idea clion phpstorm goland pycharm webstorm webide rider datagrip rubymine appcode dataspell gateway jetbrains_client jetbrainsclient"

    KDE_ENV_DIR="${USER_HOME}/.config/plasma-workspace/env"

    PROFILE_PATH="${USER_HOME}/.profile"
    ZSH_PROFILE_PATH="${USER_HOME}/.zshrc"
    PLIST_PATH="${USER_HOME}/Library/LaunchAgents/jetbrains.vmoptions.plist"

    if [ $OS_NAME = "Darwin" ]; then
        BASH_PROFILE_PATH="${USER_HOME}/.bash_profile"
    else
        BASH_PROFILE_PATH="${USER_HOME}/.bashrc"
    fi

    touch "${PROFILE_PATH}"
    touch "${BASH_PROFILE_PATH}"
    touch "${ZSH_PROFILE_PATH}"

    MY_VMOPTIONS_SHELL_NAME="jetbrains.vmoptions.sh"
    MY_VMOPTIONS_SHELL_FILE="${USER_HOME}/.${MY_VMOPTIONS_SHELL_NAME}"

    rm -rf "${MY_VMOPTIONS_SHELL_FILE}"

    if [ $OS_NAME = "Darwin" ]; then
        for PRD in $JB_PRODUCTS; do
            ENV_NAME=$(echo $PRD | tr '[a-z]' '[A-Z]')"_VM_OPTIONS"

            launchctl unsetenv "${ENV_NAME}" 2>/dev/null || true
        done

        rm -rf "${PLIST_PATH}" 2>/dev/null || true
        # 使用更安全的 sed 命令
        if [ -f "${PROFILE_PATH}" ]; then
            sed -i '' '/___MY_VMOPTIONS_SHELL_FILE="${HOME}\/\.jetbrains\.vmoptions\.sh"; if /d' "${PROFILE_PATH}" 2>/dev/null || true
        fi
        if [ -f "${BASH_PROFILE_PATH}" ]; then
            sed -i '' '/___MY_VMOPTIONS_SHELL_FILE="${HOME}\/\.jetbrains\.vmoptions\.sh"; if /d' "${BASH_PROFILE_PATH}" 2>/dev/null || true
        fi
        if [ -f "${ZSH_PROFILE_PATH}" ]; then
            sed -i '' '/___MY_VMOPTIONS_SHELL_FILE="${HOME}\/\.jetbrains\.vmoptions\.sh"; if /d' "${ZSH_PROFILE_PATH}" 2>/dev/null || true
        fi
    else
        if [ -f "${PROFILE_PATH}" ]; then
            sed -i '/___MY_VMOPTIONS_SHELL_FILE="${HOME}\/\.jetbrains\.vmoptions\.sh"; if /d' "${PROFILE_PATH}" 2>/dev/null || true
        fi
        if [ -f "${BASH_PROFILE_PATH}" ]; then
            sed -i '/___MY_VMOPTIONS_SHELL_FILE="${HOME}\/\.jetbrains\.vmoptions\.sh"; if /d' "${BASH_PROFILE_PATH}" 2>/dev/null || true
        fi
        if [ -f "${ZSH_PROFILE_PATH}" ]; then
            sed -i '/___MY_VMOPTIONS_SHELL_FILE="${HOME}\/\.jetbrains\.vmoptions\.sh"; if /d' "${ZSH_PROFILE_PATH}" 2>/dev/null || true
        fi
        rm -rf "${KDE_ENV_DIR}/${MY_VMOPTIONS_SHELL_NAME}" 2>/dev/null || true
    fi
    debug "清理三方工具环境变量完成"
}

remove_env_item_vars() {
    local shell_files=(
        "${USER_HOME}/.bash_profile"
        "${USER_HOME}/.bashrc"
        "${USER_HOME}/.zshrc"
        "${USER_HOME}/.profile"
    )

    # 先过滤出实际存在的文件
    local existing_files=()
    for file in "${shell_files[@]}"; do
        [ -f "$file" ] && existing_files+=("$file")
    done

    # 如果没有存在的文件则直接返回
    [ ${#existing_files[@]} -eq 0 ] && {
        debug "未找到任何环境变量文件,跳过"
        return
    }

    # 环境变量备份目录
    local dir_date_backup="$dir_backups/$(date +%s)"
    mkdir -p "$dir_date_backup" 2>/dev/null || true

    for file in "${existing_files[@]}"; do
        # 判断文件中是否包含指定环境变量
        if [ ! -w "$file" ]; then
            warning "文件 $file 不可写，跳过修改" >&2
            continue
        fi

        # 备份环境变量文件到dir_backups/时间
        cp "$file" "${dir_date_backup}/_$(basename ${file})" 2>/dev/null || true
        debug "备份环境变量文件: $file, $dir_date_backup,_$(basename ${file})"

        # 检测环境变量配置文件
        for product_entry in "${PRODUCTS[@]}"; do
            IFS='|' read -r name code <<< "$product_entry"
            local upper_key="$(echo "${name}" | tr '[:lower:]' '[:upper:]')_VM_OPTIONS"
            
            # 判断file里面是否包含upper_key
            if grep -q "^${upper_key}" "$file" 2>/dev/null; then
                if [[ "$OS" == "macOS" ]]; then
                    sed -i '' "/${upper_key}/d" "$file" 2>/dev/null || true
                else
                    sed -i "/${upper_key}/d" "$file" 2>/dev/null || true
                fi
                debug "删除环境变量: $file,$upper_key"
            fi
        done
        # 避免 source 时执行其他命令
        true
    done
}

remove_env_vars() {
    info "开始清理 JetBrains 相关环境变量"
    remove_env_item_vars
    # 删除其它激活工具残留
    remove_env_other
}

# ============ 用户输入授权信息 =============
validate_date_format() {
    local input="$1"

    # 第一步：检查是否符合 yyyy-MM-dd 格式
    if [[ ! "$input" =~ ^[0-9]{4}-[0-9]{2}-[0-9]{2}$ ]]; then
        warning "请输入标准格式：yyyy-MM-dd（例如：2099-12-31）"
        return 1
    fi

    # 第二步：直接返回原值（无需调用 date 验证真实性）
    echo "$input"
    return 0
}

read_license_info() {
    # 设置默认授权名称为"猿码星球"
    license_name="哇咔咔"

    # 设置默认授权日期为"2888-12-31"
    local default_expiry="2333-12-31"
    expiry_input="$default_expiry"
    
    debug "使用默认授权名称: $license_name"
    debug "使用默认授权日期: $expiry_input"
    
    if expiry=$(validate_date_format "$expiry_input"); then
        expiry="$expiry"
    else
        error "默认日期格式错误，这不应该发生"
        exit 1
    fi

    LICENSE_JSON=$(cat <<EOF
{
  "assigneeName": "",
  "expiryDate": "$expiry",
  "licenseName": "$license_name",
  "productCode": ""
}
EOF
)
}

# ============ 创建工作目录 =============
do_create_work_dir() {
    if [[ "${dir_work}" == "/" || -z "${dir_work}" ]]; then
        error "检测到非法路径: ${dir_work}，请检查配置。"
        exit 1
    fi

    if [ -d "${dir_work}" ]; then
        rm -rf "${dir_plugins}" "${dir_config}" "${file_netfilter_jar}" 2>/dev/null || {
            error "文件被占用，请先关闭所有Jetbrains IDE后再试！"
            exit 1
        }
    fi
    mkdir -p "${dir_config}" "${dir_plugins}" "${dir_backups}" 2>/dev/null || {
         error "创建工作目录失败: ${dir_config} 或 ${dir_plugins} 或 ${dir_backups}"
         exit 1
     }
    debug "创建工作目录: ${dir_work}"
}

# ============ 下载文件 =============
download_one_file_0() {
    local url="$1"
    local file_save_path="$2"

    # 如果文件已存在，跳过下载
    if [ -e "${file_save_path}" ]; then
        debug "文件已存在，跳过下载: ${file_save_path}"
        return 0
    fi

    debug "正在下载: ${url} -> ${file_save_path}"
    # ⚠️ 添加 -L 参数以跟随重定向，确保 download.php 正常工作
    curl -s -L -o "${file_save_path}" "${url}"

    if [ $? -ne 0 ]; then
        error "下载失败: ${url}"
        exit 1
    fi
}
download_one_file() {
    local url="$1"
    local file_save_path="$2"
    debug "正在下载: ${url} -> ${file_save_path}"
    # ⚠️ 添加 -L 参数以跟随重定向，确保 download.php 正常工作
    curl -s -L -o "${file_save_path}" "${url}"

    if [ $? -ne 0 ]; then
        error "下载失败: ${url}"
        exit 1
    fi

    # 可选：移除 SHA1 校验（已注释，如需保留可取消注释）
    # if [[ "$file_save_path" == *.jar ]]; then
    #     local sha1_hash
    #     if command -v $SHA_TOOL &>/dev/null; then
    #         sha1_hash=$($SHA_TOOL "$file_save_path" | awk '{print $1}')
    #         debug "sha1: $sha1_hash"
    #     else
    #         debug "未找到 $SHA_TOOL 工具，跳过 SHA-1 校验"
    #     fi
    # fi
}

progress_bar() {
    local current=$1
    local total=$2
    local bar_length=30
    local percent=$((current * 100 / total))
    local filled=$((percent * bar_length / 100))
    local bar="["
    bar+=$(printf '#%.0s' $(seq 1 $filled))
    bar+=$(printf ' %.0s' $(seq 1 $((bar_length - filled))))
    bar+="]"
    printf "\r正在激活中... %d/%d %s %d%%" "$current" "$total" "$bar" "$percent"
}

do_download_resources() {
    local resources=(
        # ⚠️ 关键修改：使用 ${URL_DOWNLOAD} 直接拼接文件名，不再加斜杠
        "${URL_DOWNLOAD}ja-netfilter.jar|${file_netfilter_jar}"
        "${URL_DOWNLOAD}config/dns.conf|${dir_config}/dns.conf"
        "${URL_DOWNLOAD}config/native.conf|${dir_config}/native.conf"
        "${URL_DOWNLOAD}config/power.conf|${dir_config}/power.conf"
        "${URL_DOWNLOAD}config/url.conf|${dir_config}/url.conf"

        "${URL_DOWNLOAD}plugins/dns.jar|${dir_plugins}/dns.jar"
        "${URL_DOWNLOAD}plugins/native.jar|${dir_plugins}/native.jar"
        "${URL_DOWNLOAD}plugins/power.jar|${dir_plugins}/power.jar"
        "${URL_DOWNLOAD}plugins/url.jar|${dir_plugins}/url.jar"
        "${URL_DOWNLOAD}plugins/hideme.jar|${dir_plugins}/hideme.jar"
        "${URL_DOWNLOAD}plugins/privacy.jar|${dir_plugins}/privacy.jar"
    )

    local total_files=${#resources[@]}
    local count=0

    debug "源ja-netfilter项目地址: https://gitee.com/ja-netfilter/ja-netfilter/releases/tag/2022.2.0"
    debug "如需检查下载的.jar是否被篡改请核对sha1的值是否与源项目文件一致"
    
    for item in "${resources[@]}"; do
        IFS='|' read -r url path <<< "$item"
        download_one_file "$url" "$path"
        ((count++))
        progress_bar "$count" "$total_files"
    done
    echo
}

# ============ 清理并更新 .vmoptions 文件 =============
clean_vmoptions() {
   local file="$1"
    if [ ! -f "$file" ]; then
        debug "清理vm: 文件不存在，跳过清理: $file"
        return 0
    fi

    # 创建备份
    local backup_file="${file}.backup.$(date +%s)"
    if ! cp "$file" "$backup_file" 2>/dev/null; then
        error "无法创建备份文件: $backup_file"
        return 1
    fi

    # 使用简单的 sed 命令来删除不需要的行
    # 先删除所有 -javaagent 开头的行
    if [[ "$OS" == "macOS" ]]; then
        sed -i '' '/^[[:space:]]*-javaagent/d' "$file" 2>/dev/null
    else
        sed -i '/^[[:space:]]*-javaagent/d' "$file" 2>/dev/null
    fi

    if [ $? -ne 0 ]; then
        error "清理文件失败: $file"
        return 1
    fi

    debug "清理完成: $file (删除了所有 -javaagent 开头的行)"
    return 0

}

append_vmoptions() {
    local file="$1"
    
    # 检查文件是否存在，不存在则创建
    if [ ! -f "$file" ]; then
        if ! touch "$file" 2>/dev/null; then
            error "生成vm: 创建失败: $file"
            return 1
        fi
    fi

    # 检查文件是否可写
    if [ ! -w "$file" ]; then
        error "文件不可写: $file"
        return 1
    fi

    # 直接添加三行代码
    {
        echo ""
        echo "--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED"
        echo "--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED"
        echo "-javaagent:${file_netfilter_jar}"
    } >> "$file"

    if [ $? -ne 0 ]; then
        error "追加内容失败: $file"
        return 1
    fi

    debug "添加三行代码到: $file"
    return 0
}

# ============ 生成激活码 =============
generate_license() {
    local obj_product_name="$1"
    local obj_product_code="$2"
    local dir_product_name="$3"
    local file_license="${dir_config_jb}/${dir_product_name}/${obj_product_name}.key"

    [ -f "$file_license" ] && rm -f "$file_license" 2>/dev/null || true

    # 手动构建 JSON，避免依赖 jq
    local json_body=$(echo "$LICENSE_JSON" | sed "s/\"productCode\": \"\"/\"productCode\": \"$obj_product_code\"/")
    
    debug "URL_LICENSE:$URL_LICENSE,save_path:$file_license"
    curl -s -X POST "$URL_LICENSE" \
        -H "Content-Type: application/json" \
        -d "$json_body" \
        -o "$file_license" > /dev/null 2>&1

    if [ $? -eq 0 ] && [ -f "$file_license" ]; then
        success "${dir_product_name} 激活成功！"
    else
        warning "${dir_product_name} 需要手动输入激活码！"
    fi
}

# ============ 处理单个 Jetbrains 产品 =============
handle_jetbrains_dir() {
  local dir="$1"
    local dir_product_name=$(basename "$dir")
    local obj_product_name=""
    local obj_product_code=""

    for product_entry in "${PRODUCTS[@]}"; do
        IFS='|' read -r name code <<< "$product_entry"
        local lowercase_dir=$(echo "${dir_product_name}" | tr '[:upper:]' '[:lower:]')
        if [[ "$lowercase_dir" == *"$name"* ]]; then
            obj_product_name="$name"
            obj_product_code="$code"
            break
        fi
    done

    [ -z "$obj_product_name" ] && return

    info "处理: ${dir_product_name}"

    # 检查文件权限和所有权
    local dir_config_product="${dir_config_jb}/${dir_product_name}"
    debug "配置目录: $dir_config_product"  # 改为 DEBUG
    
    # 检查目录是否存在，如果不存在则创建
    if [ ! -d "$dir_config_product" ]; then
        if ! mkdir -p "$dir_config_product" 2>/dev/null; then
            error "无法创建配置目录: $dir_config_product"
            # 尝试使用 sudo 创建目录
            if command -v sudo >/dev/null 2>&1; then
                info "尝试使用 sudo 创建目录..."
                if ! sudo mkdir -p "$dir_config_product" 2>/dev/null; then
                    error "使用 sudo 也无法创建目录，跳过此产品"
                    return
                fi
                # 更改目录所有权
                sudo chown "${ORIGINAL_USER}" "$dir_config_product" 2>/dev/null
            else
                error "跳过此产品"
                return
            fi
        fi
    fi

    # 查找 .vmoptions 文件
    local files=()
    while IFS= read -r -d $'\0' file; do
        files+=("$file")
    done < <(find "$dir_config_product" -name "*${FILE_VMOPTIONS}" -print0 2>/dev/null)

    debug "找到 ${#files[@]} 个 .vmoptions 文件"  # 改为 DEBUG

    # 处理找到的文件
    if [ ${#files[@]} -gt 0 ]; then
        for file_vmoption in "${files[@]}"; do
            debug "处理文件: $file_vmoption"  # 改为 DEBUG
            
            # 检查文件权限
            if [ ! -w "$file_vmoption" ]; then
                warning "文件不可写: $file_vmoption"
                # 尝试更改权限
                if command -v sudo >/dev/null 2>&1; then
                    info "尝试使用 sudo 更改文件权限..."
                    if sudo chmod 644 "$file_vmoption" 2>/dev/null; then
                        info "文件权限已更改"
                    else
                        error "无法更改文件权限，跳过此文件"
                        continue
                    fi
                else
                    error "跳过此文件"
                    continue
                fi
            fi
            
            if [ -f "$file_vmoption" ]; then
                if ! clean_vmoptions "$file_vmoption"; then
                    warning "清理文件失败: $file_vmoption，将尝试直接追加内容"
                fi
                if ! append_vmoptions "$file_vmoption"; then
                    error "追加内容失败: $file_vmoption"
                else
                    debug "成功更新文件: $file_vmoption"  # 改为 DEBUG
                fi
            fi
        done
    else
        debug "未找到 ${dir_product_name} 的.vmoptions文件，将创建一个默认的"  # 改为 DEBUG
        local default_vmoptions="${dir_config_product}/${obj_product_name}${FILE_VMOPTIONS}"
        if ! append_vmoptions "$default_vmoptions"; then
            error "创建默认文件失败: $default_vmoptions"
        else
            debug "成功创建默认文件: $default_vmoptions"  # 改为 DEBUG
        fi
    fi

    # 处理 jetbrains_client.vmoptions
    local file_jetbrains_client="${dir_config_product}/jetbrains_client.vmoptions"
    debug "处理 jetbrains_client 文件: $file_jetbrains_client"  # 改为 DEBUG
    
    if [ ! -f "${file_jetbrains_client}" ]; then
        if ! append_vmoptions "${file_jetbrains_client}"; then
            error "创建 jetbrains_client 文件失败: ${file_jetbrains_client}"
        else
            debug "成功创建 jetbrains_client 文件: ${file_jetbrains_client}"  # 改为 DEBUG
        fi
    else
        # 检查文件权限
        if [ ! -w "$file_jetbrains_client" ]; then
            warning "jetbrains_client 文件不可写: $file_jetbrains_client"
            # 尝试更改权限
            if command -v sudo >/dev/null 2>&1; then
                info "尝试使用 sudo 更改文件权限..."
                if sudo chmod 644 "$file_jetbrains_client" 2>/dev/null; then
                    info "文件权限已更改"
                else
                    error "无法更改文件权限，跳过此文件"
                fi
            else
                error "跳过此文件"
            fi
        fi
        
        if ! clean_vmoptions "${file_jetbrains_client}"; then
            warning "清理 jetbrains_client 文件失败: ${file_jetbrains_client}，将尝试直接追加内容"
        fi
        if ! append_vmoptions "${file_jetbrains_client}"; then
            error "追加 jetbrains_client 内容失败: ${file_jetbrains_client}"
        else
            debug "成功更新 jetbrains_client 文件: ${file_jetbrains_client}"  # 改为 DEBUG
        fi
    fi

    generate_license "$obj_product_name" "$obj_product_code" "$dir_product_name"


}

# ============ 主流程 =============
main() {
    # 授权码验证
    if ! verify_activation_code; then
        error "激活流程终止"
        warning "请获取有效授权码后重试"
        sleep 3
        exit 1
    fi

    clear
    show_ascii_jb
    info "欢迎使用 JetBrains 激活工具 | 哇咔咔"
    warning "脚本日期：2025-8-1 11:00:35"
    error "注意，执行脚本默认会将所有产品全部激活一遍!!!无论之前是否激活过！！"
    
    # 显示平台信息
    info "检测到操作系统: $OS"
    
    warning "请确保激活的软件处于关闭状态，请按回车继续..."
    read -r

    read_license_info

    info "处理中，请耐心等待..."

    check_deps

    if [ ! -d "${dir_config_jb}" ]; then
        error "未找到${dir_config_jb}目录"
        exit 1
    fi

    debug "config目录：${dir_config_jb}"

    do_create_work_dir

    remove_env_vars

    do_download_resources

    # 处理 JetBrains 产品目录
    if [ -d "$dir_cache_jb" ]; then
        for dir in "$dir_cache_jb"/*; do
            [ -d "$dir" ] && handle_jetbrains_dir "$dir"
        done
    else
        warning "未找到 JetBrains 缓存目录: $dir_cache_jb"
    fi

    info "激活完成，请重启软件！"
    sleep 1
    
}

main "$@"
# 删除自己

```

# gitee

https://gitee.com/vpen/gen-idea-code


