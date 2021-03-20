---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011124"
---
| Vlastnost | Popis |
|:--- |:---|
|**accountName** | Název účtu úložiště Příklad: `mystorageaccount`.  |
|**requestUrl** | Adresa URL, která je požadována. |
|**userAgentHeader** | Hodnota **hlavičky User-Agent** v uvozovkách. Příklad: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Hodnota hlavičky **odkazujícího** serveru Příklad: `http://contoso.com/about.html`.|
|**ID žádosti klienta** | Hodnota hlavičky **x-MS-Client-Request-ID** žádosti. Příklad: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**značk** | Identifikátor ETag vráceného objektu v uvozovkách Příklad: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Celkový čas vyjádřený v milisekundách k provedení požadované operace. Tato hodnota nezahrnuje latenci sítě (čas pro čtení příchozího požadavku a odeslání odpovědi žadateli). Příklad: `22`. |
|**serviceType** | Služba přidružená k této žádosti Například: `blob` , `table` , `files` nebo `queue` . |
|**operationCount** | Počet všech protokolovaných operací, které jsou součástí žádosti. Tento počet začíná indexem `0` . Některé požadavky vyžadují více než jednu operaci. Většina požadavků provádí pouze jednu operaci. Příklad: `1`. |
|**requestHeaderSize** | Velikost hlavičky požadavku vyjádřená v bajtech Příklad: `578`. <br>Pokud je žádost neúspěšná, tato hodnota může být prázdná. |
|**requestBodySize** | Velikost paketů požadavků vyjádřených v bajtech, které jsou čteny službou úložiště. <br> Příklad: `0`. <br>Pokud je žádost neúspěšná, tato hodnota může být prázdná.  |
|**responseHeaderSize** | Velikost hlavičky odpovědi vyjádřená v bajtech Příklad: `216`. <br>Pokud je žádost neúspěšná, tato hodnota může být prázdná.  |
|**responseBodySize** | Velikost paketů odpovědí zapsaných službou úložiště (v bajtech). Pokud je žádost neúspěšná, tato hodnota může být prázdná. Příklad: `216`.  |
|**requestMd5** | Hodnota buď v hlavičce **Content-MD5** , nebo v hlavičce **x-MS-Content-MD5** v žádosti. Hodnota hash MD5 zadaná v tomto poli představuje obsah v žádosti. Příklad: `788815fd0198be0d275ad329cafd1830`. <br>Toto pole může být prázdné.  |
|**serverMd5** | Hodnota hash MD5 vypočítaná službou úložiště Příklad: `3228b3cf1069a5489b298446321f8521`. <br>Toto pole může být prázdné.  |
|**Časposledníúpravy** | Čas poslední změny (LMT) vráceného objektu.  Příklad: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Toto pole je prázdné pro operace, které mohou vracet více objektů. |
|**conditionsUsed** | Středníkem oddělený seznam párů klíč-hodnota, které reprezentují podmínku. Podmínky mohou být následující: <li> If-Modified – od <li> Pokud-nezměněno – od <li> If-Match <li> If-None-Match  <br> Příklad: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Hodnota záhlaví Content-Length pro požadavek odeslanou službě úložiště. Pokud byla žádost úspěšná, tato hodnota se rovná requestBodySize. Pokud je žádost neúspěšná, tato hodnota nesmí být rovna requestBodySize nebo může být prázdná. |
|**tlsVersion** | Verze TLS použitá v souvislosti s připojením žádosti. Příklad: `TLS 1.2`. |
|**smbTreeConnectID** | Protokol SMB (Server Message Block) **treeConnectId** byl vytvořen v době připojení ke stromu. Příklad: `0x3` |
|**smbPersistentHandleID** | ID trvalého popisovače z žádosti o vytvoření SMB2, která je trvale připojená k síti.  Odkazováno v [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 jako **SMB2_FILEID. Trvalé**. Příklad: `0x6003f` |
|**smbVolatileHandleID** | Nestálé ID popisovače z žádosti o vytvoření SMB2, která se recykluje při opětovném připojení k síti.  Odkazováno v [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 jako **SMB2_FILEID. Volatile**. Příklad: `0xFFFFFFFF00000065` |
|**smbMessageID** | Relativní připojení **MessageID**. Příklad: `0x3b165` |
|**smbCreditsConsumed** | Příchozí nebo odchozí výstup spotřebovaný žádostí v jednotkách 64 KB. Příklad: `0x3` |
|**smbCommandDetail** | Další informace o této konkrétní žádosti místo obecného typu požadavku. Příklad: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | Identifikátor **FileID** přidružený k souboru nebo adresáři.  Zhruba se podobá systému souborů NTFS FileId. Příklad: `0x9223442405598953` |
|**smbSessionID** | SMB2 **SessionID** vytvořená v čase nastavení relace. Příklad: `0x8530280128000049` |
|**smbCommandMajor UInt32** | Hodnota v **příkazu SMB2_HEADER.** V současné době je to číslo mezi 0 a 18 včetně. Příklad: `0x6` |
|**smbCommandMinor** | Podtřída **SmbCommandMajor**, kde je to vhodné. Příklad: `DirectoryCloseAndDelete` |