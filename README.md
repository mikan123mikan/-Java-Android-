使用言語：Python  twitter apiを使って特定のワードを検索し、20件のツイートを集めてテキスト化し、任意のハードディスク上のフォルダにワードファイルとして保存するプログラムです。使用時にOAuth認証が必要です。

from requests_oauthlib import OAuth1Session    //Oauth認証でtwitter apiを使用します
import json
import datetime, time, sys　　　　　　　　　　　　//日付を取得するモジュール
import docx　　　　　　　　　　　　　　　　　　　　//ワードファイルを扱うモジュール
import os
import send2trash

doc = docx.Document()

CK = '自分のパスワードを入れます'         # Consumer Key
CS = '自分のパスワードを入れます'         # Consumer Secret
AT = '自分のパスワードを入れます' 　　　　 # Access Token
AS = '自分のパスワードを入れます'　　　    # Access Token Secret

session = OAuth1Session(CK, CS, AT, AS)
 
url = 'https://api.twitter.com/1.1/search/tweets.json'
res = session.get(url, params = {'q':u'prtimes.jp/main', 'count':30})　　//prtimesの所に自分が検索したいワードを入れます
 
# ステータスコード確認
if res.status_code != 200:　　　　　　　　　　　　　　　　　//twitter apiの使用制限の通知をします
    print ("Twitter API Error: %d" % res.status_code)
    sys.exit(1)

print ('アクセス可能回数 %s' % res.headers['X-Rate-Limit-Remaining'])   //この部分は無くても動作します
print ('リセット時間 %s' % res.headers['X-Rate-Limit-Reset'])
sec = int(res.headers['X-Rate-Limit-Reset'])\
           - time.mktime(datetime.datetime.now().timetuple())
print ('リセット時間 （残り秒数に換算） %s' % sec)
 
# テキスト部

res_text = json.loads(res.text)
for tweet in res_text['statuses']:
    print ('-----')
    print (tweet['created_at'])
    print (tweet['text'])
    string_a = tweet['created_at']
    string_b = tweet['text']
    doc.add_paragraph(string_a)　　　//1行ずつワードに書き込みます。
    doc.add_paragraph(string_b)　　　
    
doc.save(r'C:\Users\ユーザ名\ディレクトリ名\aaa.docx')         //ここは仮にaaaという名前にしています。
os.chdir(r'C:\Users\ユーザ名\ディレクトリ名\')
now = datetime.datetime.now()
old = "aaa.docx"　　　　　　　　　　　　　　　　　　　　　　　　//51行で書いたaaaと同じ名前にします。
new = "{0:%Y%m%d_%H%M%Sprtimes}.docx".format(now)　　　　　//prtimesの所は先ほど指定した検索したいワードにします
os.rename(old,new)　　　　　　　　　　　　　　　　　　　　　　　//名前を現在の年、日付、時刻に変更します
