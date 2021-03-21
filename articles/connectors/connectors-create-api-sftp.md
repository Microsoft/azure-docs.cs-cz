---
title: Připojit k účtu SFTP (zastaralé)
description: Automatizace úloh a procesů, které sledují, vytváří, spravují, odesílají a přijímají soubory pro server SFTP pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ROBOTS: NOINDEX
ms.openlocfilehash: 70fb956af7ff45c7b54f04d7ed441ec39f9d80a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673805"
---
# <a name="monitor-create-and-manage-sftp-files-in-azure-logic-apps"></a>Monitorování, vytváření a Správa souborů SFTP v Azure Logic Apps

> [!IMPORTANT]
> Použijte prosím [konektor SFTP-SSH](../connectors/connectors-sftp-ssh.md) , protože konektor SFTP je zastaralý. V návrháři aplikace logiky už nemůžete vybírat triggery a akce protokolu SFTP.

Chcete-li automatizovat úlohy, které sledují, vytváří, odesílají a přijímají soubory na serveru [Secure protokol FTP (File Transfer Protocol) (SFTP)](https://www.ssh.com/ssh/sftp/) , můžete pomocí Azure Logic Apps a konektoru SFTP sestavit a automatizovat pracovní postupy integrace. SFTP je síťový protokol, který poskytuje přístup k souborům, přenos souborů a správu souborů přes jakýkoliv spolehlivý datový proud. Tady je několik ukázkových úloh, které můžete automatizovat:

* Monitorování při přidání nebo změně souborů
* Získat, vytvořit, kopírovat, aktualizovat, zobrazit a odstranit soubory.
* Získá obsah souboru a metadata.
* Extrahuje archivy do složek.

Můžete použít triggery, které sledují události na vašem serveru SFTP a zpřístupňují výstup ostatním akcím. Na serveru SFTP můžete použít akce, které provádějí různé úlohy. V aplikaci logiky můžete mít také jiné akce, které využívají výstup z akcí SFTP. Pokud například pravidelně načítáte soubory ze serveru SFTP, můžete odesílat e-mailové výstrahy týkající se těchto souborů a jejich obsahu pomocí konektoru Office 365 Outlook Connector nebo konektoru Outlook.com. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Omezení

Konektor SFTP zpracovává pouze soubory, které jsou *50 MB nebo menší* , a nepodporuje vytváření [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). U větších souborů použijte [konektor SFTP-SSH](../connectors/connectors-sftp-ssh.md). Rozdíly mezi konektorem SFTP a konektorem SFTP-SSH najdete v článku [porovnání protokolu SFTP-SSH versus SFTP](../connectors/connectors-sftp-ssh.md#comparison) v článku o protokolu SFTP-SSH.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Vaše adresa serveru SFTP a přihlašovací údaje účtu, které vaší aplikaci logiky umožní přístup k vašemu účtu SFTP. K použití protokolu [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) potřebujete také přístup k privátnímu klíči SSH a k heslu privátního klíče SSH.

  > [!NOTE]
  >
  > Konektor SFTP podporuje tyto formáty privátních klíčů: OpenSSH, ssh.com a.
  >
  > Když vytváříte aplikaci logiky a přidáte do ní aktivační událost nebo požadovanou akci SFTP, budete muset zadat informace o připojení pro váš server SFTP. 
  > Pokud používáte privátní klíč SSH, nezapomeňte ***zkopírovat*** klíč ze souboru privátního klíče SSH a tento klíč ***Vložit*** do podrobností o připojení, ***nemusíte ručně zadávat ani upravovat klíč***, což by mohlo způsobit selhání připojení. 
  > Další informace najdete v dalších krocích v tomto článku.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k vašemu účtu SFTP. Pokud chcete začít s triggerem protokolu SFTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci SFTP, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="how-sftp-triggers-work"></a>Jak funguje aktivace protokolem SFTP

Služba SFTP spouští dotazování systému souborů SFTP a hledání všech souborů, které byly od posledního dotazování změněny. Některé nástroje umožňují zachovat časové razítko při změně souborů. V těchto případech je nutné zakázat tuto funkci, aby mohla Trigger fungovat. Tady je několik běžných nastavení:

| Klient SFTP | Akce |
|-------------|--------|
| WinSCP | Přejít na **Možnosti**  >  **Předvolby**  >  **přenos**  >  **Upravit**  >  **zachovat časové razítko**  >  **Zakázat** |
| FileZilly | Přejít na **přenos**–  >  zachovat zablokovaná **Časová razítka přenesených souborů**  >   |
|||

Pokud aktivační událost najde nový soubor, aktivační událost zkontroluje, jestli je nový soubor hotový, a ne částečně napsaný. Soubor může mít například probíhající změny, když aktivační událost kontroluje souborový server. Aby nedošlo k vrácení částečně napsaného souboru, aktivační událost zapisuje časové razítko pro soubor, který má poslední změny, ale tento soubor okamžitě nevrátí. Aktivační událost vrátí soubor pouze při opakovaném dotazování serveru. V některých případech může toto chování způsobit zpoždění až dvojnásobku intervalu dotazování triggeru.

## <a name="connect-to-sftp"></a>Připojení k SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné aplikace logiky zadejte do vyhledávacího pole "SFTP" jako filtr. V seznamu triggery vyberte aktivační událost, kterou chcete.

   -nebo-

   Pro existující aplikace logiky v rámci posledního kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. Do vyhledávacího pole zadejte "SFTP" jako filtr. V seznamu akce vyberte akci, kterou chcete.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Zadejte potřebné informace pro vaše připojení.

   > [!IMPORTANT]
   >
   > Když zadáte privátní klíč SSH do vlastnosti **privátního klíče SSH** , postupujte podle těchto dalších kroků, které vám pomůžou zajistit, aby byla pro tuto vlastnost k dispozici úplná a správná hodnota. 
   > Neplatný klíč způsobí selhání připojení.

   I když můžete použít libovolný textový editor, tady je ukázkový postup, který ukazuje, jak správně zkopírovat a vložit klíč pomocí Notepad.exe jako příklad.

   1. V textovém editoru otevřete soubor privátního klíče SSH. Tyto kroky používají jako příklad program Poznámkový blok.

   1. V nabídce **Úpravy** poznámkového bloku vyberte **Vybrat vše**.

   1. Vyberte **Upravit**  >  **kopii**.

   1. V triggeru nebo aktivované akci SFTP vložte *úplný* klíč, který jste zkopírovali do vlastnosti **privátního klíče SSH** , který podporuje více řádků. Ujistěte se, **_že jste vložili_*klíč _. _* tento _klíč nemusíte zadávat ani upravovat ručně_**.

1. Až skončíte s zadáním podrobností o připojení, vyberte **vytvořit**.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="examples"></a>Příklady

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Trigger protokolu SFTP: při přidání nebo úpravě souboru

Tato aktivační událost spustí pracovní postup aplikace logiky při přidání nebo změně souboru na serveru SFTP. Můžete například přidat podmínku, která zkontroluje obsah souboru a získá obsah na základě toho, jestli obsah splňuje zadanou podmínku. Pak můžete přidat akci, která získá obsah souboru, a tento obsah vložit do složky na serveru SFTP.

**Podnikový příklad**: tuto aktivační událost můžete použít k monitorování složky SFTP pro nové soubory, které reprezentují objednávky zákazníků. Pak můžete použít akci SFTP, například **získat obsah souboru** , abyste získali obsah objednávky pro další zpracování a uložení tohoto pořadí v databázi objednávek.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Akce protokolu SFTP: získání obsahu

Tato akce načte obsah ze souboru na serveru SFTP. Například můžete přidat Trigger z předchozího příkladu a podmínku, že obsah souboru musí splňovat. Pokud je podmínka pravdivá, bude možné spustit akci, která načte obsah.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](../data-factory/connector-sftp.md)konektoru.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)