---
title: Připojení k serveru SFTP pomocí SSH – Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh, které monitorování, vytvářet, spravovat, odeslání a přijetí soubory pro SFTP server pomocí SSH a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
tags: connectors
ms.date: 01/15/2019
ms.openlocfilehash: 807a99a8cac7326648ff4aa91b9fcdeb35de196a
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910179"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorování, vytvářet a spravovat soubory protokolu SFTP pomocí SSH a Azure Logic Apps

Automatizace úloh, které monitorování, vytvářet, odesílat a přijímat soubory na [rozhraní Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) server s použitím [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokol, můžete vytvářet a automatizovat integrace pracovní postupy s využitím Azure Logic Apps a konektor SFTP-SSH. SFTP je síťový protokol, který poskytuje přístup k souborům, přenosu souborů a správu přes jakýkoli spolehlivý datový proud. Tady je příklad úlohy, které můžete automatizovat: 

* Monitorování, když jsou soubory přidány nebo změněny.
* Získat, vytvořit, kopírovat, přejmenovat, aktualizovat seznam a odstranit soubory.
* Vytváření složek.
* Získáte obsah souboru a metadata.
* Extrahujte archivy do složek.

Ve srovnání s [konektor SFTP](../connectors/connectors-create-api-sftp.md), konektor SFTP-SSH může číst nebo zapisovat soubory až *1 GB* dílky velikostí správy dat v 50 MB. Pro soubory větší než 1 GB, můžete použít akce [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). Další rozdíly najdete v tématu [porovnání SFTP SSH a SFTP](#comparison) dále v tomto článku.

Můžete použít aktivační události, které sledovat události na vašem serveru SFTP a zpřístupnit výstup dalších akcí. Můžete použít akce, které provádění různých úloh na vašem serveru SFTP. Také můžete mít další akce ve vaší aplikaci logiky použít výstup z akcí SFTP. Například pokud pravidelně Načtení souborů ze serveru SFTP, můžete odeslat e-mailová upozornění o těchto souborech a jejich obsah s použitím konektoru Office 365 Outlook nebo konektor Outlook.com.
Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Porovnání SFTP SSH a SFTP

Tady jsou další hlavní rozdíly mezi konektoru SFTP-SSH a SFTP konektor, kde konektor SFTP-SSH má tyto možnosti:

* Používá <a href="https://github.com/sshnet/SSH.NET" target="_blank"> **SSH.NET** </a> knihovny, která je open source knihovna Secure Shell (SSH), který podporuje .NET. 

  > [!NOTE]
  >
  > Podporuje konektor SFTP-SSH *pouze* těchto privátních klíčů, formátů, algoritmy a otisky prstů:
  > 
  > * **Privátní klíče formáty**: RSA (Rivest Shamir Adleman) a klíče algoritmu DSA (algoritmu Digital Signature Algorithm) ve formátu OpenSSH a ssh.com
  > * **Algoritmy šifrování**: DES-EDE3-CBC, DES-EDE3-CFB DES-CBC, AES-128-CBC, AES-192-CBC a AES-256-CBC
  > * **Otisk prstu**: MD5

* Čtení nebo zápis do souborů *1 GB* velikosti ve srovnání s konektoru SFTP, ale zpracovává data v části 50 MB, ne 1 GB kusy. Pro soubory větší než 1 GB, můžete také použít akce [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). V současné době aktivační události nepodporují dělením dat do bloků.

* Poskytuje **vytvořit složku** akce, která vytvoří složku v zadané složce na serveru SFTP.

* Poskytuje **přejmenování souboru** akce, která přejmenuje soubor na serveru SFTP.

* Ukládá do mezipaměti, připojení k serveru SFTP *až 1 hodinu*, což zvyšuje výkon a snižuje počet pokusů při připojování k serveru. Chcete-li nastavit dobu trvání pro toto chování ukládání do mezipaměti, upravte <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank"> **ClientAliveInterval** </a> vlastnosti v konfiguraci SSH na vašem serveru SFTP. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* SFTP server adresu a účet pověření, které umožňují přístup k účtu SFTP aplikace logiky. Budete potřebovat přístup k privátní klíč SSH a heslo privátního klíče SSH. 

  > [!IMPORTANT]
  >
  > Podporuje konektor SFTP-SSH *pouze* tyto formáty privátního klíče, algoritmy a otisky prstů:
  > 
  > * **Privátní klíče formáty**: RSA (Rivest Shamir Adleman) a klíče algoritmu DSA (algoritmu Digital Signature Algorithm) ve formátu OpenSSH a ssh.com
  > * **Algoritmy šifrování**: DES-EDE3-CBC, DES-EDE3-CFB DES-CBC, AES-128-CBC, AES-192-CBC a AES-256-CBC
  > * **Otisk prstu**: MD5
  >
  > Při vytváření aplikace logiky po přidání SFTP-SSH triggeru nebo akce, které chcete, budete muset zadat informace o připojení k serveru SFTP. 
  > Pokud používáte privátní klíč SSH, ujistěte se, že jste ***kopírování*** klíče z vašeho SSH soubor privátního klíče, a ***vložte*** tento klíč do podrobností připojení ***není ručně zadat nebo upravit klíč***, což může způsobit selhání připojení. 
  > Další informace najdete v dalších krocích v tomto článku.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k účtu SFTP. Začít s SFTP-SSH aktivační událost, [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci SFTP-SSH, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="connect-to-sftp-with-ssh"></a>Připojení k protokolu SFTP pomocí protokolu SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte "sftp ssh" jako filtr. V seznamu triggerů vyberte trigger, který chcete. 

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 
   Do vyhledávacího pole zadejte "sftp ssh" jako filtr. 
   V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Zadejte potřebné podrobnosti o připojení.

   > [!IMPORTANT] 
   >
   > Když zadáte privátní klíč SSH v **privátní klíč SSH** vlastnosti těchto dodatečných kroků, které pomohou Ujistěte se, že poskytují kompletní a správný hodnotu pro tuto vlastnost. 
   > Neplatný klíč způsobí selhání připojení.
   
   I když můžete použít libovolný textový editor, tady jsou ukázkové kroky, které ukazují, jak správně zkopírujte a vložte váš klíč pomocí Notepad.exe jako příklad.
    
   1. Otevření souboru privátního klíče SSH v textovém editoru. 
   Tyto kroky používají stejně jako v příkladu programu Poznámkový blok.

   1. V poznámkovém bloku na **upravit** nabídce vyberte možnost **Vybrat vše**.

   1. Vyberte **upravit** > **kopírování**.

   1. V protokolu SFTP SSH triggeru nebo akce, které jste přidali, vložte *kompletní* klíče, které jste zkopírovali do **privátní klíč SSH** vlastnost, která podporuje více řádků. 
   ***Ujistěte se, že vložíte*** klíč. ***Není ručně zadat nebo upravit klíč***.

1. Až budete mít zadání podrobností o připojení, zvolit **vytvořit**.

1. Teď zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="trigger-limits"></a>Omezení aktivační událost

Triggery SFTP-SSH fungovat dotazování systém souborů protokolu SFTP a vyhledáním každý soubor, který se změnil od posledního cyklického dotazování. Některé nástroje umožňují zachovat časové razítko, když se změní soubory. V těchto případech budete muset zakázat tuto funkci, aktivační událost můžete pracovat. Tady jsou některé běžné nastavení:

| Klient protokolu SFTP | Akce | 
|-------------|--------| 
| Winscp | Přejděte na **možnosti** > **Předvolby** > **přenos** > **upravit**  >  **Zachovat časové razítko** > **zakázat** |
| FileZilla | Přejděte na **přenos** > **zachovat časová razítka přenášených souborů** > **zakázat** | 
||| 

Když aktivační události vyhledá nový soubor, trigger zkontroluje, zda nový soubor úplné a částečně napsané. Soubor může například mít změny v průběhu při trigger bude kontrolovat souborového serveru. Aktivační událost se pokud chcete vyhnout, vrací částečně napsané souborů, poznámky časové razítko pro soubor, který obsahuje poslední změny, ale nevrací okamžitě tento soubor. Aktivační událost vrátí soubor pouze v případě, že dotazování serveru znovu. Toto chování může způsobit zpoždění, které je až dvakrát triggeru interval dotazování. 

Pokud se požaduje obsah souboru, aktivační události Nezískávat soubory větší než 50 MB. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru: 

* Pomocí aktivační události, která vrací vlastnosti souboru, například **kdy soubor se přidá nebo upraví (jen vlastnosti)**.

* Postupujte podle aktivační událost s akci, která načte celý soubor, jako například **získat obsah souboru pomocí cesty**, a mít akci použít [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Příklady

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - aktivovat SSH: Když je přidán nebo upraven soubor

Tato aktivační událost se spustí pracovní postup aplikace logiky souboru při přidání nebo změně na SFTP server. Například můžete přidat podmínku, která zkontroluje obsah souboru a získá obsah založen na tom, jestli obsah splňují zadanou podmínku. Potom přidáte akci, která získá obsah souboru a umístí tento obsah do složky na serveru SFTP. 

**Příklad organizace**: Tato aktivační událost můžete použít k monitorování složky aplikace SFTP pro nové soubory, které představují objednávek zákazníků. Můžete pak použít akci SFTP jako **získat obsah souboru** tak získat obsah pořadí pro další zpracování a uložení do databáze objednávek tohoto pořadí.

Pokud se požaduje obsah souboru, aktivační události Nezískávat soubory větší než 50 MB. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru: 

* Pomocí aktivační události, která vrací vlastnosti souboru, například **kdy soubor se přidá nebo upraví (jen vlastnosti)**.

* Postupujte podle aktivační událost s akci, která načte celý soubor, jako například **získat obsah souboru pomocí cesty**, a mít akci použít [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md).

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH akce: Získání obsahu pomocí cesty

Tato akce načte obsah ze souboru na SFTP server. Takže například můžete přidat aktivační událost z předchozího příkladu a podmínku, která musí splňovat obsahu souboru. Pokud je podmínka pravdivá, můžete spustit akci, která získá obsah. 

Pokud se požaduje obsah souboru, aktivační události Nezískávat soubory větší než 50 MB. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru: 

* Pomocí aktivační události, která vrací vlastnosti souboru, například **kdy soubor se přidá nebo upraví (jen vlastnosti)**.

* Postupujte podle aktivační událost s akci, která načte celý soubor, jako například **získat obsah souboru pomocí cesty**, a mít akci použít [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/sftpconnector/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
