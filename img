import ftplib
import os
import pyautogui
from tkinter import *

# pyinstaller main.py -F --upx-dir C:\DevStudy\upx401w64\


def get_config_data():
    _config_data = {
        'ftp_ip': None,
        'ftp_port': None,
        'ftp_id': None,
        'ftp_pwd': None,
        'ftp_folder': None,
        'ftp_url': None,
    }
    try:
        with open(os.getcwd() + '/config.dat') as file:
            input_data = file.read().splitlines()
    except FileNotFoundError:
        print('config.dat 파일을 찾을 수 없습니다.')
        exit(0)

    for key in _config_data:
        for elem in input_data:
            if elem.startswith(key):
                _config_data[key] = elem.split(key + ":")[1].strip()
    if ~_config_data['ftp_folder'].endswith('/'):
        _config_data['ftp_folder'] = _config_data['ftp_folder'] + '/'
    if ~_config_data['ftp_url'].endswith('/'):
        _config_data['ftp_url'] = _config_data['ftp_url'] + '/'
    _config_data['ftp_url'] = _config_data['ftp_url'] + _config_data['ftp_folder']
    return _config_data


def set_input_values(input_values, min_uid, max_uid, tab1, tab2, tab3):
    input_values['max_uid'] = int(max_uid)
    input_values['min_uid'] = int(min_uid)
    input_values['tab'] = [tab1, tab2, tab3]


def remove_img(_session, _full_file_name, _file_name):
    try:
        _session.delete(_full_file_name)
        print(_file_name + ': 파일 삭제 성공!')
    except Exception as e:
        print(_file_name, '삭제 실패')
        print(e)


def close_job(_wait_window):
    _wait_window.destroy()


def main_job():

    input_values = {
        "max_uid": int(0),
        "min_uid": int(0)
    }

    wait_window = Tk()

    wait_window.wm_attributes("-topmost", 1)
    wait_window.title("대기")
    wait_window.geometry("290x121")

    row = 1
    column = 1

    row += 1
    column = 1
    label1 = Label(wait_window, width=10, text="상품번호>=")
    label1.grid(row=row, column=1)
    column += 1
    entry1 = Entry(wait_window, width=20)
    entry1.insert(0, "0")
    entry1.grid(row=row, column=column, columnspan=3)

    row += 1
    column = 1
    label2 = Label(wait_window, width=10, text="상품번호<=")
    label2.grid(row=row, column=column)
    column += 1
    entry2 = Entry(wait_window, width=20)
    entry2.insert(0, "0")
    entry2.grid(row=row, column=column, columnspan=3)

    row += 1
    column = 1
    label3 = Label(wait_window, width=10, text="수정 탭")
    label3.grid(row=row, column=column)

    column += 1
    chk1_var = IntVar()
    chk1_var.set(1)
    chk1 = Checkbutton(wait_window, text="대표", variable=chk1_var, width=2)
    chk1.grid(row=row, column=column)

    column += 1
    chk2_var = IntVar()
    chk2_var.set(1)
    chk2 = Checkbutton(wait_window, text="옵션", variable=chk2_var, width=2)
    chk2.grid(row=row, column=column)

    column += 1
    chk3_var = IntVar()
    chk3_var.set(1)
    chk3 = Checkbutton(wait_window, text="상세", variable=chk3_var, width=2)
    chk3.grid(row=row, column=column)
    button1 = Button(wait_window, width=40, text="진행",
                     command=lambda: [
                         set_input_values(input_values, entry1.get(), entry2.get(),
                                          chk1_var.get(), chk2_var.get(), chk3_var.get()),
                         wait_window.destroy()
                     ]
                     )
    row += 1
    column = 1
    button1.grid(row=row, column=column, columnspan=4)
    button2 = Button(wait_window, width=40, text="종료", command=lambda: close_job(wait_window))

    row += 1
    column = 1
    button2.grid(row=row, column=column, columnspan=4)

    wait_window.mainloop()

    job_count = int(0)
    success = int(0)
    fail = int(0)

    session = ftplib.FTP()
    session.connect(config_data['ftp_ip'], int(config_data['ftp_port']))
    session.login(config_data['ftp_id'], config_data['ftp_pwd'])
    session.encoding = 'utf-8'

    tab = input_values['tab']

    uid = input_values['min_uid']
    while True:
        if input_values['max_uid'] != 0 and uid > input_values['max_uid']:
            break
        job_count += 1
        uid = str(uid)
        print('[' + uid + '] 상품 작업중')
        ftp_files = session.nlst(config_data['ftp_folder'])
        ftp_file_count = 0
        for full_file_name in ftp_files:
            file_name = full_file_name[len(config_data['ftp_folder'])::]
            if full_file_name.startswith(config_data['ftp_folder'] + uid + '_'):
                for (ind, tab_chk) in enumerate(tab):
                    ind = str(ind)
                    if file_name[len(uid) + len('yymmdd') + 2] == ind:
                        if tab_chk:
                            ftp_file_count += 1
                            try:
                                remove_img(session, full_file_name, file_name)
                            except Exception:
                                print(file_name + ' 이미지 삭제 실패')
                                fail += 1
        success += 1
        uid = int(uid) + 1

    pyautogui.alert(f'{job_count}개의 상품 작업 완료! 성공: {success}, 실패: {fail}')
    print(f'{job_count}개의 상품 작업 완료! 성공: {success}, 실패: {fail}')


if __name__ == '__main__':

    config_data = get_config_data()
    while True:
        try:
            main_job()
        except Exception as e:
            print('작업 중 오류가 발생하였습니다.')
            print(e)
            break
