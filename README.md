使用言語：Python  
twitter apiを使って特定のワードを検索し、20件のツイートを集めてテキスト化し、  
任意のハードディスク上のフォルダにワードファイルとして保存するプログラムです。  
使用時にOAuth認証が必要です。

from requests_oauthlib import OAuth1Session  //Oauth認証でtwitter apiを使用します<br>
import json <br>
import datetime, time, sys　　　　　　　　　　　　//日付を取得するモジュール<br>
import docx　　　　　　　　　　　　　　　　　　　　//ワードファイルを扱うモジュール<br>
import os <br>
import send2trash <br>

doc = docx.Document() <br>

CK = '自分のパスワードを入れます'         # Consumer Key <br>
CS = '自分のパスワードを入れます'         # Consumer Secret <br>
AT = '自分のパスワードを入れます' 　　　　 # Access Token <br>
AS = '自分のパスワードを入れます'　　　    # Access Token Secret <br>

session = OAuth1Session(CK, CS, AT, AS) <br>
 
url = 'https://api.twitter.com/1.1/search/tweets.json' <br>
res = session.get(url, params = {'q':u'prtimes.jp/main', 'count':30})//prtimesの所に自分が検索したいワードを入れます <br>
 
// ステータスコード確認 <br>
if res.status_code != 200:　　　　　　　　　　　　　　　　　//twitter apiの使用制限の通知をします <br>
    print ("Twitter API Error: %d" % res.status_code) <br>
    sys.exit(1) <br>

print ('アクセス可能回数 %s' % res.headers['X-Rate-Limit-Remaining'])   //この部分は無くても動作します <br>
print ('リセット時間 %s' % res.headers['X-Rate-Limit-Reset']) <br>
sec = int(res.headers['X-Rate-Limit-Reset'])\ <br>
           - time.mktime(datetime.datetime.now().timetuple()) <br>
print ('リセット時間 （残り秒数に換算） %s' % sec) <br>
 
// テキスト部 <br>

res_text = json.loads(res.text) <br>
for tweet in res_text['statuses']: <br>
    print ('-----') <br>
    print (tweet['created_at']) <br>
    print (tweet['text']) <br>
    string_a = tweet['created_at'] <br>
    string_b = tweet['text'] <br> 
    doc.add_paragraph(string_a)　　　//1行ずつワードに書き込みます。 <br>
    doc.add_paragraph(string_b)　　　 <br>
    
doc.save(r'C:\Users\ユーザ名\ディレクトリ名\aaa.docx')         //ここは仮にaaaという名前にしています。 <br>
os.chdir(r'C:\Users\ユーザ名\ディレクトリ名\') <br>
now = datetime.datetime.now()
old = "aaa.docx"　　　　　　//3行前に書いたaaaと同じ名前にします。 <br>
new = "{0:%Y%m%d_%H%M%Sprtimes}.docx".format(now) //prtimesの所は先ほど指定した検索したいワードにします <br>
os.rename(old,new)　　　　　　　　　　　　　　　　　　　//名前を現在の年、日付、時刻に変更します <br>
