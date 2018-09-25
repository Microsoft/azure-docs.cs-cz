---
title: Připojení k účtu SFTP z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které monitorování, vytvářet, spravovat, odesílat a přijímat soubory pro SFTP server pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: cfee4f06479d2504b88f4a5d5a0a2417154e3b03
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47065103"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Monitorování, vytvářet a spravovat soubory protokolu SFTP pomocí konektoru Azure Logic Apps a SFTP-SSH

S Azure Logic Apps a konektor SFTP-SSH, můžete vytvořit automatizovaných úloh a pracovních postupů, které monitorování, vytvářet, odesílat a přijímat soubory pomocí vašeho účtu na [SFTP](https://www.ssh.com/ssh/sftp/) serverem a také další akce, například:

* Monitorování, když jsou soubory přidány nebo změněny.
* Získat, vytvořit, kopírovat, přejmenovat, aktualizovat seznam a odstranit soubory.
* Vytvořte složku.
* Získáte obsah souboru a metadata.
* Extrahujte archivy do složek.

Můžete použít aktivační události, které odpovědi ze serveru SFTP a zpřístupnit výstup dalších akcí. Akce ve službě logic apps můžete použít k provádění úloh se soubory na vašem serveru SFTP. Také můžete mít další akce pomocí výstupu z akce SFTP. Například pokud pravidelně Načtení souborů ze serveru SFTP, můžete odeslat e-mailu o těchto souborech a jejich obsah s použitím konektoru Office 365 Outlook nebo konektor Outlook.com.
Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="sftp-ssh-versus-sftp"></a>SFTP SSH a SFTP

Tady je několik klíčových rozdílů mezi konektoru SFTP-SSH a [SFTP](../connectors/connectors-create-api-sftp.md) konektoru. Konektor SFTP-SSH poskytuje tyto možnosti:

* Používá <a href="https://github.com/sshnet/SSH.NET" target="_blank"> **SSH.NET** </a> knihovny, která je open source knihovna Secure Shell (SSH) pro .NET.

* Poskytuje podporu pro velké soubory až **1 GB**. Konektor může číst nebo zapisovat soubory, které jsou až do velikosti 1 GB.

* Poskytuje **vytvořit složku** akce, která vytvoří složku v zadané složce na serveru SFTP.

* Poskytuje **přejmenování souboru** akce, která přejmenuje soubor na serveru SFTP.

* Ukládá do mezipaměti připojení k serveru SFTP, což zvyšuje výkon a snižuje počet pokusů o připojení na serveru. 

  Můžete řídit dobu trvání používá pro ukládání do mezipaměti připojení nastavením <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank"> **ClientAliveInterval** </a> vlastnost na vašem serveru SFTP. 

## <a name="how-trigger-polling-works"></a>Jak aktivovat funguje cyklického dotazování

Triggery SFTP-SSH fungovat dotazování systém souborů protokolu SFTP a vyhledáním každý soubor, který se změnil od posledního cyklického dotazování. Některé nástroje umožňují zachovat časové razítko při změně souborů, takže v těchto případech je nutné zakázat tuto funkci pro aktivační událost pro práci. Tady jsou některé běžné nastavení:

| Klient protokolu SFTP | Akce | 
|-------------|--------| 
| Winscp | Předvolby → možnosti... Upravit přenos → →... Zakázat → zachovat časové razítko → |
| Filezilly | Zakázat přenos → zachovat časová razítka přenesené soubory → | 
||| 

Když aktivační události vyhledá nový soubor, trigger zkontroluje, zda nový soubor úplné a částečně napsané. Soubor může například mít změny v průběhu při trigger bude kontrolovat souborového serveru. Aktivační událost se pokud chcete vyhnout, vrací částečně napsané souborů, poznámky časové razítko pro soubor, který obsahuje poslední změny, ale nevrací okamžitě tento soubor. Aktivační událost vrátí soubor pouze v případě, že dotazování serveru znovu. Toto chování může způsobit zpoždění, které je až dvakrát triggeru interval dotazování. 

Pokud se požaduje obsah souboru, aktivační událost nenačítá soubory větší než 50 MB. Aby se získaly soubory větší než 50 MB, postupujte podle tohoto vzoru:

* Pomocí aktivační události, která vrací vlastnosti souboru, například **kdy soubor se přidá nebo upraví (jen vlastnosti)**. 
* Postupujte podle aktivační událost s akci, která načte celý soubor, jako například **získat obsah souboru pomocí cesty**.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Vaše SFTP server adresu a účet přihlašovacím údajům hostitele, který autorizuje aplikace logiky k vytvoření připojení a přístup k účtu SFTP.

  Zkopírujte a vložte celý obsah privátního klíče SSH do **privátní klíč SSH** vlastnost podle víceřádkovém formátu. 
  Tady je ukázka kroky, které ukazují, jak zajistit privátní klíč SSH pomocí Notepad.exe:
    
  1. Otevřete soubor privátního klíče SSH v Notepad.exe
  2. Na **upravit** nabídce vyberte možnost **Vybrat vše**.
  3. Vyberte **upravit** > **kopírování**.
  4. Při vytváření připojení, v **privátní klíč SSH** vlastnost, vložte klíč. Neupravujte ručně **privátní klíč SSH** vlastnost.

     Konektor používá SSH.NET knihovny, který podporuje tyto SSH formáty privátní klíče a jenom MD5 – otisk prstu:

     * RSA 
     * DSA

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k účtu SFTP. Spuštění pomocí aktivační událost SFTP [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci SFTP, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="connect-to-sftp"></a>Připojení k protokolu SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "sftp". V seznamu triggerů vyberte trigger, který chcete. 

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "sftp". 
   V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Zadejte nezbytné podrobnosti připojení a pak zvolte **vytvořit**.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="examples"></a>Příklady

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Aktivační událost SFTP: při přidání nebo změně souboru

Tato aktivační událost se spustí pracovní postup aplikace logiky Pokud trigger rozpozná souboru při přidání nebo změně na SFTP server. Takže například že můžete přidat podmínku, která zkontroluje obsah souboru a rozhodne, zda tento obsah získali založené na tom, jestli tento obsah splňují zadanou podmínku. Nakonec můžete přidat akci, která získá obsah souboru a vložit obsah do složky na serveru SFTP. 

**Příklad organizace**: Tato aktivační událost můžete použít k monitorování složky aplikace SFTP pro nové soubory, které představují objednávek zákazníků. Můžete pak použít akci SFTP jako **získat obsah souboru**, abyste mohli získat obsah pořadí pro další zpracování a uložení této objednávky do databáze objednávek.

### <a name="sftp-action-get-content"></a>Akce SFTP: získání obsahu

Tato akce načte obsah ze souboru na SFTP server. Takže například můžete přidat aktivační událost z předchozího příkladu a podmínku, která musí splňovat obsahu souboru. Pokud je podmínka pravdivá, můžete spustit akci, která získá obsah. 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/sftpconnector/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)