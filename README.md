# version_check
배포용 버전 체크

url = f"https://api.github.com/repos/{GITHUB_REPO}/releases"

---------------------------------------------------------
py 에서 상단
```
APP_VERSION = "3.3.2"
GITHUB_REPO = "eriwpa/version_check"
```
Rs에서 상단
```
const APP_VERSION: &str = "KakaoPage_Downloader_3.3.1";  
const GITHUB_REPO: &str = "eriwpa/version_check";
```

```
# =============== 버전 체크 ===============

def check_update():
    """GitHub 릴리즈 태그와 버전 비교, 없으면 차단"""
    try:
        url = f"https://api.github.com/repos/{GITHUB_REPO}/releases"
        resp = requests.get(url, timeout=5)
        if resp.status_code != 200:
            return True
        
        data = resp.json()
        all_tags = [r.get("tag_name", "") for r in data]
        
        if not all_tags:
            return True
        
        if APP_VERSION in all_tags:
            return True
        
        app = QApplication.instance()
        if app is None:
            app = QApplication(sys.argv)
        
        msg = QMessageBox()
        msg.setIcon(QMessageBox.Warning)
        msg.setWindowTitle("업데이트 필요")
        msg.setText(
            f"현재 버전: {APP_VERSION}\n\n"
            f"👻 새 버전으로 업데이트해주세요."
        )
        msg.setStandardButtons(QMessageBox.Ok)
        msg.exec_()
        
        return False
        
    except Exception:
        app = QApplication.instance()
        if app is None:
            app = QApplication(sys.argv)
        
        msg = QMessageBox()
        msg.setIcon(QMessageBox.Critical)
        msg.setWindowTitle("연결 실패")
        msg.setText("버전 확인을 위해 인터넷 연결이 필요합니다.")
        msg.setStandardButtons(QMessageBox.Ok)
        msg.exec_()
        
        return False

# =============== 메인 실행 ===============

def main():
    app = QApplication(sys.argv)
    app.setStyle('Fusion')
    
    if not check_update():
        sys.exit(0)
    
    window = EpubConverterWindow()
    window.show()
    
    sys.exit(app.exec_())

if __name__ == '__main__':
    main()
```

```
# =============== 버전 체크 ===============
def check_update():
    """GitHub 릴리즈 태그와 버전 비교, 없으면 차단"""
    try:
        url = f"https://api.github.com/repos/{GITHUB_REPO}/releases"
        resp = requests.get(url, timeout=5)
        if resp.status_code != 200:
            return True
        
        data = resp.json()
        all_tags = [r.get("tag_name", "") for r in data]
        
        if not all_tags:
            return True
        
        if APP_VERSION in all_tags:
            return True
        
        print(f"\n⚠️  업데이트 필요")
        print(f"현재 버전: {APP_VERSION}")
        print(f"👻 새 버전으로 업데이트해주세요.")
        input("\nEnter를 누르면 종료됩니다...")
        return False
        
    except Exception:
        print("\n❌ 연결 실패")
        print("버전 확인을 위해 인터넷 연결이 필요합니다.")
        input("\nEnter를 누르면 종료됩니다...")
        return False

# =============== 메인 ===============

if __name__ == '__main__':

    try:
        if not check_update():
            sys.exit(1)
        main()

    except KeyboardInterrupt:
        print("\n\n프로그램이 사용자에 의해 중단되었습니다.")
        sys.exit(0)
```

```
fn check_update() -> Result<bool> {
    let url = format!("https://api.github.com/repos/{}/releases", GITHUB_REPO);
    let client = reqwest::blocking::Client::new();
    
    let resp = match client.get(&url)
        .header("User-Agent", "rust-app")
        .timeout(Duration::from_secs(5))
        .send() {
        Ok(r) => r,
        Err(_) => {
            println!("\n❌ 연결 실패");
            println!("버전 확인을 위해 인터넷 연결이 필요합니다.");
            utils::press_enter_to_continue();
            return Ok(false);
        }
    };

    if !resp.status().is_success() {
        return Ok(true);
    }

    let data: Vec<Value> = match resp.json() {
        Ok(d) => d,
        Err(_) => return Ok(true),
    };

    let all_tags: Vec<String> = data.iter()
        .filter_map(|r| r.get("tag_name").and_then(|t| t.as_str()).map(|s| s.to_string()))
        .collect();

    if all_tags.is_empty() {
        return Ok(true);
    }

    if all_tags.contains(&APP_VERSION.to_string()) {
        return Ok(true);
    }

    println!("\n⚠️  업데이트 필요");
    println!("현재 버전: {}", APP_VERSION);
    println!("👻 새 버전으로 업데이트해주세요.");
    utils::press_enter_to_continue();
    Ok(false)
}
```

```
fn main() -> Result<()> {
    check_expiry()?;
    if !check_update()? {       // 추가
        std::process::exit(1);  // 추가
    }                           // 추가
    
    ctrlc::set_handler(|| {
```
```
    println!("　　{}", APP_VERSION);
```
