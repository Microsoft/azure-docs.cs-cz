---
title: Zachování seznamů ACL souborů, atributů a časových razítek pomocí Azure Data Box
description: Seznamy ACL, časová razítka a atributy zachované během kopírování dat prostřednictvím protokolu SMB do Azure Data Box. Kopírování metadat pomocí nástrojů pro kopírování dat v systému Windows a Linux.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950308"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Zachování seznamů ACL souborů, atributů a časových razítek pomocí Azure Data Box

Azure Data Box umožňuje zachovat seznamy řízení přístupu (ACL), časová razítka a atributy souborů při odesílání dat do Azure. Tento článek popisuje metadata, která můžete přenést při kopírování dat do Data Box prostřednictvím protokolu SMB (Server Message Block) k nahrání do souborů Azure. 

K dispozici jsou konkrétní kroky pro kopírování metadat pomocí nástrojů pro kopírování dat v systému Windows a Linux. Při přenosu dat do úložiště objektů BLOB se metadata nezachovají.

V tomto článku se seznamy ACL, časová razítka a atributy souborů, které se přenesou, označují souhrnně jako *metadata*.

## <a name="transferred-metadata"></a>Přenesená metadata

Následující metadata se přenesou při odeslání dat z Data Box do souborů Azure.

#### <a name="timestamps"></a>Časová razítka

Následující časová razítka se přenesou:
- CreationTime
- LastWriteTime

Následující časové razítko není převedeno:
- LastAccessTime
  
#### <a name="file-attributes"></a>Atributy souboru

Atributy souborů v souborech i adresářích jsou přenášeny, pokud není uvedeno jinak.

Následující atributy souborů jsou přeneseny:
- FILE_ATTRIBUTE_READONLY (pouze soubor)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (pouze adresář)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (pouze soubor)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Následující atributy souboru se nepřenášejí:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
V adresářích nejsou přenášeny atributy jen pro čtení.

#### <a name="acls"></a>Seznamy ACL

Všechny seznamy ACL pro adresáře a soubory, které zkopírujete do Data Box přes SMB, se zkopírují a přenesou. Přenosy zahrnují jak volitelné seznamy řízení přístupu (DACL), tak i systémové seznamy řízení přístupu (SACL). Pro Linux se přenesou jenom seznamy ACL pro Windows NT.

Seznamy řízení přístupu (ACL) se nepřenášejí během kopírování dat přes systém souborů NTS (Network File System) a při použití služby kopírování dat pro přenos dat. Služba kopírování dat čte data přímo z vašich sdílených složek a nemůže číst seznamy ACL.

I když nástroj pro kopírování dat nekopíruje seznamy řízení přístupu (ACL), budou se výchozí seznamy ACL adresářů a souborů přenášet do souborů Azure. Výchozí seznamy ACL mají oprávnění pro předdefinovaný účet správce, účet SYSTEM a uživatelský účet sdílené složky SMB, který se použil k připojení a zkopírování dat v Data Box.

Seznamy ACL obsahují popisovače zabezpečení s následujícími vlastnostmi: seznamy řízení přístupu (ACL), vlastník, skupina, SACL.

Ve výchozím nastavení je povolený přenos seznamů ACL. Toto nastavení můžete chtít zakázat v místním webovém uživatelském rozhraní v Data Box. Další informace najdete v tématu [použití místního webového uživatelského rozhraní ke správě data box a data box Heavy](./data-box-local-web-ui-admin.md).

> [!NOTE]
> Soubory s seznamy ACL, které obsahují řetězce ACE podmíněného řízení přístupu (ACE), se nekopírují. Jedná se o známý problém. Pokud to chcete obejít, zkopírujte tyto soubory do sdílené složky Azure Files ručně připojením sdílené složky a následným použitím nástroje pro kopírování, který podporuje kopírování seznamů ACL.

## <a name="copying-data-and-metadata"></a>Kopírování dat a metadat

Pro přenos seznamů ACL, časových razítek a atributů pro data použijte následující postupy ke zkopírování dat do Data Box. 

### <a name="windows-data-copy-tool"></a>Nástroj pro kopírování dat Windows

Chcete-li kopírovat data do svého Data Box pomocí protokolu SMB, použijte nástroj pro kopírování souborů kompatibilní s protokolem SMB, jako je například `robocopy` . Následující vzorový příkaz zkopíruje všechny soubory a adresáře a přenáší metadata spolu s daty.

Pokud používáte `/copyall` možnost nebo `/dcopy:DAT` , ujistěte se, že požadovaná oprávnění operátora zálohování nejsou zakázaná. Další informace najdete v tématu [použití místního webového uživatelského rozhraní ke správě data box a data box Heavy](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

kde:

|Možnost |Popis |
|------------------- | ----- |
|`/copyall` |Zkopíruje všechny atributy.|
|`/e`      |Zkopíruje podadresáře, včetně prázdných adresářů.         |
|`/dcopy:DAT`  |Kopíruje data, atributy a časová razítka. Poznámka: možnost/DCOPY: DAT se musí použít k přenosu `CreationTime` do adresářů. |
|`/r:3`    |Určuje 3 opakování při neúspěšných kopiích.         |
|`/w:60`   |Určuje dobu čekání 60 sekund mezi opakovanými pokusy.         |
|`/is`     |Zahrne stejné soubory.         |
|`/nfl`    |Nezaznamenává názvy souborů.         |
|`/ndl`    |Nezaznamenává názvy adresářů.        |
|`/np`     |Nezobrazuje průběh operace kopírování.         |
|`/MT:32 or 64`  |Používá Multithreading s vlákny 32 nebo 64.           |
|`/fft`    |Omezuje členitost časového razítka pro libovolný systém souborů.        |
|`/log+:<LogFile>`  |Připojí výstup k existujícímu souboru protokolu.|

Další informace o těchto `robocopy` parametrech najdete v tématu [kurz: kopírování dat do Azure Data box přes protokol SMB.](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Nástroj pro kopírování dat pro Linux

Přenos metadat v systému Linux je proces se dvěma kroky. Nejprve zkopírujete zdrojová data pomocí nástroje `rsync` , například, který nekopíruje metadata. Po zkopírování dat můžete metadata zkopírovat pomocí nástroje, jako je například `smbcacls` nebo `cifsacl` . 

Následující vzorové příkazy dělají první krok a kopírují data pomocí `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Další kroky

- [Kopírování dat do Azure Data Boxu přes protokol SMB](./data-box-deploy-copy-data.md)