<div align="center">

<img alt="LOGO" src="https://i.imgur.com/WKXJDZL.png" width="300" height="300" />
  
# Tweetcord

Discord的Twitter通知機器人

[**English**](./README.md) | [**繁體中文**](./README_zh.md)

</div>

## 📝簡介

Tweetcord是一個discord機器人，它使用tweety-ns模組讓你在discord上接收特定Twitter用戶的推文更新。你只需要設置想要關注的Twitter用戶和discord頻道，Tweetcord就會自動將推文發送到指定的頻道，這樣你就不會錯過任何重要的消息。🐦

## ✨功能

<details>
   <summary>

### 截圖

   </summary>
👇當你關注的用戶發布了新的推文，你的伺服器也會收到通知。

![](https://i.imgur.com/SXITM0a.png)

</details>

<details>
   <summary>

### 指令

   </summary>

👉 `/add notifier` `username` `channel` | `mention`

| 參數 | 類型 | 描述 |
| --------- | ----- | ----------- |
| `username` | str | 你想要開啟通知的Twitter用戶的用戶名 |
| `channel` | discord.TextChannel | 機器人發送通知的頻道 |
| `mention` | discord.Role | 通知時提及的身分組 |

👉 `/remove notifier` `username` `channel`

| 參數 | 類型 | 描述 |
| --------- | ----- | ----------- |
| `username` | str | 你想要關閉通知的Twitter用戶的用戶名 |
| `channel` | discord.TextChannel | 設置為發送通知的頻道 |

👉 `/list users`

- 列出所有當前伺服器開啟通知的Twitter用戶

</details>

## 📥安裝

在運行機器人之前，你需要安裝必要的模組。

```shell
pip install -r requirements.txt
```

在某些作業系統中，你可能需要使用 `pip3` 而不是 `pip` 來進行安裝。

## ⚡使用

**📢本教學適用於0.3.2或更高版本。（建議：0.3.5或更高版本）**

<details>
   <summary><b>📌0.3.4升級到0.3.5請點這裡</b></summary>

在 `cogs` 資料夾創建一個python檔案並命名為 `upgrade.py`，貼上下面的程式碼並運行機器人，使用斜線指令 `/upgrade` 進行升級。升級結束後可以移除這個cog。

```py
import discord
from discord import app_commands
from core.classes import Cog_Extension
import sqlite3
import os

from src.log import setup_logger
from src.permission_check import is_administrator

log = setup_logger(__name__)

class Upgrade(Cog_Extension):

    @is_administrator()
    @app_commands.command(name='upgrade', description='upgrade to Tweetcord 0.3.5')
    async def upgrade(self, itn: discord.Interaction):
        
        await itn.response.defer(ephemeral=True)
        
        conn = sqlite3.connect(f"{os.getenv('DATA_PATH')}tracked_accounts.db")
        cursor = conn.cursor()

        cursor.executescript('ALTER TABLE channel ADD server_id TEXT')
        
        cursor.execute('SELECT id FROM channel')
        channels = cursor.fetchall()
        
        for c in channels:
            try:
                channel = self.bot.get_channel(int(c[0]))
                cursor.execute('UPDATE channel SET server_id = ? WHERE id = ?', (channel.guild.id, channel.id))
            except:
                log.warning(f'the bot cannot obtain channel: {c[0]}, but this will not cause problems with the original features. The new feature can also be used normally on existing servers.')
                

        conn.commit()
        conn.close()

        await itn.followup.send('successfully upgrade to 0.3.5, you can remove this cog.')


async def setup(bot):
    await bot.add_cog(Upgrade(bot))
```

</details>

<details>
   <summary><b>📌0.3.3升級到0.3.4請點這裡</b></summary>

因為資料庫結構更新因此必須使用以下程式碼更新資料庫結構。

```py
from dotenv import load_dotenv
import os
import sqlite3

load_dotenv()

conn = sqlite3.connect(f"{os.getenv('DATA_PATH')}tracked_accounts.db")
cursor = conn.cursor()

cursor.execute('ALTER TABLE notification ADD enabled INTEGER DEFAULT 1')

conn.commit()
conn.close()
```

</details>

### 1. 創建並配置.env文件

```env
BOT_TOKEN=YourDiscordBotToken
TWITTER_TOKEN=YourTwitterAccountAuthToken
DATA_PATH=./data/
```

你可以從cookies中獲取你的token，或是你可以探索其他獲取它的方法。

### 2. 配置configs.yml文件

所有與時間相關的配置都以秒為單位。

```yml
prefix: ''                          # 機器人命令的前綴。
activity_name: ''                   # 機器人顯示的活動名稱。
tweets_check_period: 10             # 檢查推文的頻率（不建議將此值設置得太低，以避免速率限制）。
tweets_updater_retry_delay: 300     # 當Tweets Updater遇到異常（例如速率限制）時的重試間隔。
tasks_monitor_check_period: 60      # 檢查每個任務是否正常運行的間隔，如果某個任務停止了，嘗試重新啟動。
tasks_monitor_log_period: 14400     # 將當前運行中的任務列表輸出到執行日誌的間隔。
```

### 3. 運行機器人並邀請至你的伺服器

```shell
python bot.py
```

在某些操作系統中，你可能需要使用 `python3` 而不是 `python`。

🔧機器人權限設定 `2147666944`

- [x] 讀取訊息（Read Messages/View Channels）
- [x] 發送訊息（Send Messages）
- [x] 嵌入連結（Embed Links）
- [x] 附加檔案（Attach Files）
- [x] 提及 @everyone、@here 和所有身分組（Mention Everyone）
- [x] 使用應用程式命令（Use Slash Commands）

### 4. 玩得開心

現在你可以回到Discord，並使用 `/add notifier` 指令來設置你想要接收更新的Twitter用戶！

## 💪貢獻者

感謝所有貢獻者。

[![](https://contrib.rocks/image?repo=Yuuzi261/Tweetcord)](https://github.com/Yuuzi261/Tweetcord/graphs/contributors)
