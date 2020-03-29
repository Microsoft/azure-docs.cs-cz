---
title: Připojení k účtu SFTP
description: Automatizace úloh a procesů, které monitorují, vytvářejí, spravují, odesílají a přijímají soubory pro server SFTP prostřednictvím SSH pomocí Aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: divswa, klam, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: d0da98070fa8da5403677e1a67bda75456c74d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789269"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorování, vytváření a správa souborů SFTP pomocí Azure Logic Apps

> [!IMPORTANT]
> Použijte [konektor SFTP-SSH,](../connectors/connectors-sftp-ssh.md) protože konektor SFTP je zastaralá. V Návrháři aplikací logiky již nelze vybrat aktivační události a akce SFTP.

Chcete-li automatizovat úlohy, které monitorují, vytvářejí, odesílají a přijímají soubory na serveru [SFTP (Secure File Transfer Protocol),](https://www.ssh.com/ssh/sftp/) můžete vytvářet a automatizovat pracovní postupy integrace pomocí Azure Logic Apps a konektoru SFTP. SFTP je síťový protokol, který poskytuje přístup k souborům, přenos souborů a správu souborů přes jakýkoliv spolehlivý datový proud. Zde je několik příkladů úkolů, které můžete automatizovat:

* Sledujte, kdy jsou soubory přidány nebo změněny.
* Získejte, vytvářejte, kopírujte, aktualizujte, seznamujte a odstraňujte soubory.
* Získejte obsah souboru a metadata.
* Extrahujte archivy do složek.

Můžete použít aktivační události, které monitorují události na serveru SFTP a zpřístupňují výstup jiným akcím. Můžete použít akce, které provádějí různé úkoly na serveru SFTP. Můžete mít také další akce v aplikaci logiky použít výstup z akcí SFTP. Pokud například pravidelně načítáte soubory ze serveru SFTP, můžete odesílat e-mailová upozornění na tyto soubory a jejich obsah pomocí konektoru nebo konektoru aplikace Office 365 Outlook nebo Outlook.com konektoru. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Omezení

Konektor SFTP zpracovává pouze soubory, které jsou *50 MB nebo menší* a nepodporuje [blokování zpráv](../logic-apps/logic-apps-handle-large-messages.md). U větších souborů použijte [konektor SFTP-SSH](../connectors/connectors-sftp-ssh.md). Rozdíly mezi konektorem SFTP a konektorem SFTP-SSH naleznete v článku SFTP-SSH [porovnání SFTP-SSH oproti SFTP.](../connectors/connectors-sftp-ssh.md#comparison)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa serveru SFTP a přihlašovací údaje k účtu, které umožňují aplikaci logiky přístup k účtu SFTP. Chcete-li použít protokol [Secure Shell (SSH),](https://www.ssh.com/ssh/protocol/) potřebujete také přístup k soukromému klíči SSH a k heslu soukromého klíče SSH.

  > [!NOTE]
  >
  > Konektor SFTP podporuje tyto formáty soukromých klíčů: OpenSSH, ssh.com a PuTTY
  >
  > Při vytváření aplikace logiky po přidání aktivační události SFTP nebo akce, kterou chcete, budete muset poskytnout informace o připojení pro server SFTP. 
  > Pokud používáte soukromý klíč SSH, ujistěte se, že ***zkopírujete*** klíč ze souboru soukromého klíče SSH a ***vložte*** tento klíč do podrobností o připojení, ***nezadávejte ručně nebo neupravujte klíč***, což může způsobit selhání připojení. 
  > Další informace naleznete v následujících krocích v tomto článku.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu SFTP. Chcete-li začít s aktivační událostí SFTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci SFTP, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **opakování.**

## <a name="how-sftp-triggers-work"></a>Jak spouští SFTP funguje

SFTP aktivuje práci dotazováním systému souborů SFTP a hledáním jakéhokoli souboru, který byl změněn od posledního dotazování. Některé nástroje umožňují zachovat časové razítko při změně souborů. V těchto případech je třeba tuto funkci zakázat, aby aktivační událost mohla fungovat. Zde jsou některá běžná nastavení:

| Klient SFTP | Akce |
|-------------|--------|
| Winscp | Přejít na **možnosti** >  >  > **Předvolby** > **Převést** > **Upravit Zachovat**časové**razítko****Zakázat** |
| Filezilla | Přejít na **zachovat** > **časová razítka přenesených souborů** > **Zakázat** |
|||

Když aktivační událost najde nový soubor, aktivační událost zkontroluje, zda je nový soubor dokončen a není částečně zapsán. Soubor může mít například probíhající změny, když aktivační událost zkontroluje souborový server. Aby se zabránilo vrácení částečně zapsaného souboru, aktivační událost zaznamená časové razítko souboru, který má nedávné změny, ale okamžitě tento soubor nevrátí. Aktivační událost vrátí soubor pouze při dotazování serveru znovu. Někdy toto chování může způsobit zpoždění, které je až dvojnásobek intervalu dotazování aktivační události.

## <a name="connect-to-sftp"></a>Připojení k sftp

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "sftp". V seznamu aktivačních událostí vyberte požadovanou aktivační událost.

   -nebo-

   U existujících aplikací logiky v posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. Do vyhledávacího pole zadejte jako filtr "sftp". V seznamu akcí vyberte požadovanou akci.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Poskytněte potřebné podrobnosti pro připojení.

   > [!IMPORTANT]
   >
   > Když zadáte svůj soukromý klíč SSH ve vlastnosti **soukromého klíče SSH,** postupujte podle těchto dalších kroků, které vám pomohou zajistit, že pro tuto vlastnost zadáte úplnou a správnou hodnotu. 
   > Neplatný klíč způsobí selhání připojení.

   I když můžete použít libovolný textový editor, zde jsou ukázkové kroky, které ukazují, jak správně zkopírovat a vložit klíč pomocí poznámkového bloku.exe jako příklad.

   1. Otevřete soubor soukromého klíče SSH v textovém editoru. V tomto příkladu se používá poznámkový blok.

   1. V nabídce Poznámkový blok **Upravit** vyberte **Vybrat vše**.

   1. Vyberte **Upravit** > **kopii**.

   1. V aktivační události SFTP nebo akci, kterou jste přidali, vložte *celý* klíč, který jste zkopírovali do vlastnosti **soukromého klíče SSH,** která podporuje více řádků. ***Ujistěte se, že jste vložili*** klíč. ***Nezadávejte ručně ani neupravujte klíč***.

1. Po zadání podrobností o připojení zvolte **Vytvořit**.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="examples"></a>Příklady

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Aktivační událost SFTP: Při přidání nebo úpravě souboru

Tato aktivační událost spustí pracovní postup aplikace logiky při přidání nebo změně souboru na serveru SFTP. Můžete například přidat podmínku, která zkontroluje obsah souboru a získá obsah na základě toho, zda obsah splňuje zadanou podmínku. Potom můžete přidat akci, která získá obsah souboru a umístí tento obsah do složky na serveru SFTP.

**Příklad organizace**: Tuto aktivační událost můžete použít ke sledování složky SFTP pro nové soubory, které představují objednávky zákazníků. Potom můžete použít akci SFTP, jako je **například získat obsah souboru,** takže získáte obsah objednávky pro další zpracování a uložte tuto objednávku do databáze objednávek.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Akce SFTP: Získání obsahu

Tato akce získá obsah ze souboru na serveru SFTP. Můžete například přidat aktivační událost z předchozího příkladu a podmínku, kterou musí splňovat obsah souboru. Pokud je podmínka pravdivá, akce, která získá obsah lze spustit.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/sftpconnector/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
