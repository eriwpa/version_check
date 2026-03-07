# version_check
배포용 버전 체크

url = f"https://api.github.com/repos/{GITHUB_REPO}/releases"

---------------------------------------------------------
py 에서

상단
APP_VERSION = "3.3.2"
GITHUB_REPO = "eriwpa/version_check"


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
