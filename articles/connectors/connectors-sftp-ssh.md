---
title: Připojení k serveru SFTP pomocí ssh
description: Automatizace úloh, které monitorují, vytvářejí, spravují, odesílají a přijímají soubory pro server SFTP pomocí SSH a Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 03/7/2020
tags: connectors
ms.openlocfilehash: d4ab7425c967d3a176c0a576d0be38ece1701b8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128412"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Sledování, vytváření a správa souborů SFTP pomocí Aplikací SSH a Azure Logic Apps

Chcete-li automatizovat úlohy, které monitorují, vytvářejí, odesílají a přijímají soubory na serveru [SFTP (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) pomocí protokolu [Secure Shell (SSH),](https://www.ssh.com/ssh/protocol/) můžete vytvářet a automatizovat pracovní postupy integrace pomocí Aplikací Azure Logic Apps a konektoru SFTP-SSH. SFTP je síťový protokol, který poskytuje přístup k souborům, přenos souborů a správu souborů přes jakýkoliv spolehlivý datový proud. Zde je několik příkladů úkolů, které můžete automatizovat:

* Sledujte, kdy jsou soubory přidány nebo změněny.
* Získejte, vytvářejte, kopírujte, přejmenováváte, aktualizujte, seznamujte a odstraňujte soubory.
* Vytvořte složky.
* Získejte obsah souboru a metadata.
* Extrahujte archivy do složek.

Můžete použít aktivační události, které monitorují události na serveru SFTP a zpřístupňují výstup jiným akcím. Můžete použít akce, které provádějí různé úkoly na serveru SFTP. Můžete mít také další akce v aplikaci logiky použít výstup z akcí SFTP. Pokud například pravidelně načítáte soubory ze serveru SFTP, můžete odesílat e-mailová upozornění na tyto soubory a jejich obsah pomocí konektoru nebo konektoru aplikace Office 365 Outlook nebo Outlook.com konektoru. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Rozdíly mezi konektorem SFTP-SSH a konektorem SFTP naleznete v části [Porovnání SFTP-SSH versus SFTP](#comparison) dále v tomto tématu.

## <a name="limits"></a>Omezení

* Akce SFTP-SSH, které podporují [bloků,](../logic-apps/logic-apps-handle-large-messages.md) mohou zpracovávat soubory až do velikosti 1 GB, zatímco akce SFTP-SSH, které nepodporují bloků, mohou zpracovávat soubory až do velikosti 50 MB. Přestože výchozí velikost bloku dat je 15 MB, tato velikost může dynamicky měnit, počínaje od 5 MB a postupně se zvyšuje na 50 MB maximum, na základě faktorů, jako je latence sítě, doba odezvy serveru a tak dále.

  > [!NOTE]
  > Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

  Toto adaptivní chování můžete přepsat, když místo toho [zadáte konstantní velikost bloku,](#change-chunk-size) který se má použít. Tato velikost může být v rozsahu od 5 MB do 50 MB. Předpokládejme například, že máte soubor o velikosti 45 MB a síť, která může tuto velikost souboru podporovat bez latence. Adaptivní bloků výsledkem v několika volání, spíše, že jedno volání. Chcete-li snížit počet volání, můžete zkusit nastavit velikost bloku dat 50 MB. V jiném scénáři, pokud je vypršení časového limitu aplikace logiky, například při použití bloků 15 MB, můžete zkusit zmenšit velikost na 5 MB.

  Velikost bloku je přidružena k připojení, což znamená, že můžete použít stejné připojení pro akce, které podporují bloků a pak pro akce, které nepodporují bloků. V tomto případě velikost bloku pro akce, které nepodporují bloků v rozsahu od 5 MB do 50 MB. V této tabulce jsou uvedeny akce SFTP-SSH, které podporují bloků:

  | Akce | Podpora bloků | Podpora velikosti bloku přepsání |
  |--------|------------------|-----------------------------|
  | **Kopírovat soubor** | Ne | Neuvedeno |
  | **Vytvořit soubor** | Ano | Ano |
  | **Vytvořit složku** | Neuvedeno | Neuvedeno |
  | **Odstranit dlaždici** | Neuvedeno | Neuvedeno |
  | **Extrahovat archiv do složky** | Neuvedeno | Neuvedeno |
  | **Získání obsahu souboru** | Ano | Ano |
  | **Získání obsahu souboru pomocí cesty** | Ano | Ano |
  | **Získání metadat souboru** | Neuvedeno | Neuvedeno |
  | **Získání metadat souboru pomocí cesty** | Neuvedeno | Neuvedeno |
  | **Seznam souborů ve složce** | Neuvedeno | Neuvedeno |
  | **Přejmenování souboru** | Neuvedeno | Neuvedeno |
  | **Aktualizovat soubor** | Ne | Neuvedeno |
  ||||

* Aktivační události SFTP-SSH nepodporují zasypané zprávy. Při požadavku na obsah souboru aktivační události vyberou pouze soubory, které jsou 15 MB nebo menší. Chcete-li získat soubory větší než 15 MB, postupujte podle tohoto vzoru:

  1. Použijte aktivační událost SFTP-SSH, která vrací pouze vlastnosti souboru, například **Při přidání nebo změně souboru (pouze vlastnosti).**

  1. Postupujte podle aktivační události s akcí SFTP-SSH **Get,** která čte celý soubor a implicitně používá blokování zpráv.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Porovnat SFTP-SSH versus SFTP

Zde jsou další klíčové rozdíly mezi konektorem SFTP-SSH a konektorem SFTP, kde konektor SFTP-SSH má tyto možnosti:

* Používá [knihovnu SSH.NET](https://github.com/sshnet/SSH.NET), což je knihovna s otevřeným zdrojovým kódem SSH (Open source Shell), která podporuje rozhraní .NET.

* Obsahuje akci **Vytvořit složku,** která vytvoří složku na zadané cestě na serveru SFTP.

* Obsahuje akci **Přejmenovat soubor,** která přejmenuje soubor na serveru SFTP.

* Ukládá připojení k serveru SFTP *až na 1 hodinu*, což zvyšuje výkon a snižuje počet pokusů o připojení k serveru. Chcete-li nastavit dobu trvání tohoto chování ukládání do mezipaměti, upravte vlastnost [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) v konfiguraci SSH na serveru SFTP.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa serveru SFTP a přihlašovací údaje k účtu, které umožňují aplikaci logiky přístup k účtu SFTP. Potřebujete také přístup k soukromému klíči SSH a k heslu soukromého klíče SSH. Chcete-li při nahrávání velkých souborů použít bloků, potřebujete oprávnění ke čtení i zápisu pro kořenovou složku na serveru SFTP. V opačném případě se zobrazí chyba "401 Neautorizováno".

  > [!IMPORTANT]
  >
  > Konektor SFTP-SSH podporuje *pouze* tyto formáty soukromých klíčů, algoritmy a otisky prstů:
  >
  > * **Soukromé formáty klíčů:** KLÍČE RSA (Rivest Shamir Adleman) a DSA (Digital Signature Algorithm) ve formátech OpenSSH i ssh.com. Pokud je váš soukromý klíč ve formátu souboru PuTTY (.ppk), [nejprve jej převeďte do formátu souboru OpenSSH (.pem).](#convert-to-openssh)
  >
  > * **Šifrovací algoritmy**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC a AES-256-CBC
  >
  > * **Otisk prstu**: MD5
  >
  > Po přidání aktivační události SFTP-SSH, kterou chcete do aplikace logiky, je třeba zadat informace o připojení pro server SFTP. Pokud pro toto připojení zadáte soukromý klíč SSH, ***nezadávejte jej ručně ani jej neupravujte***, což může způsobit selhání připojení. Místo toho se ujistěte, že ***zkopírujete klíč*** ze souboru soukromého klíče SSH a ***vložte*** tento klíč do podrobností o připojení. 
  > Další informace naleznete v části [Připojení k SFTP s SSH](#connect) později v tomto článku.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu SFTP. Chcete-li začít s aktivační událostí SFTP-SSH, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci SFTP-SSH, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **opakování.**

## <a name="how-sftp-ssh-triggers-work"></a>Jak spouštěče SFTP-SSH fungují

SFTP-SSH spouští práci dotazování matný systém Souborů SFTP a hledá jakýkoli soubor, který byl změněn od posledního dotazování. Některé nástroje umožňují zachovat časové razítko při změně souborů. V těchto případech je třeba tuto funkci zakázat, aby aktivační událost mohla fungovat. Zde jsou některá běžná nastavení:

| Klient SFTP | Akce |
|-------------|--------|
| Winscp | Přejít na **možnosti** >  >  > **Předvolby** > **Převést** > **Upravit Zachovat**časové**razítko****Zakázat** |
| Filezilla | Přejít na **zachovat** > **časová razítka přenesených souborů** > **Zakázat** |
|||

Když aktivační událost najde nový soubor, aktivační událost zkontroluje, zda je nový soubor dokončen a není částečně zapsán. Soubor může mít například probíhající změny, když aktivační událost zkontroluje souborový server. Aby se zabránilo vrácení částečně zapsaného souboru, aktivační událost zaznamená časové razítko souboru, který má nedávné změny, ale okamžitě tento soubor nevrátí. Aktivační událost vrátí soubor pouze při dotazování serveru znovu. Někdy toto chování může způsobit zpoždění, které je až dvojnásobek intervalu dotazování aktivační události.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Převést klíč založený na PuTTY na OpenSSH

Pokud je váš soukromý klíč ve formátu PuTTY, který používá příponu názvu souboru .ppk (PuTTY Private Key), nejprve převeďte klíč do formátu OpenSSH, který používá příponu .pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Operační systém založený na Unixu

1. Pokud nástroje PuTTY ještě nejsou ve vašem systému nainstalovány, udělejte to nyní, například:

   `sudo apt-get install -y putty`

1. Spusťte tento příkaz, který vytvoří soubor, který můžete použít s konektorem SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Například:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Operační systém Windows

1. Pokud jste tak již neučinili, [stáhněte si nejnovější nástroj PuTTY Generator (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)a poté nástroj spusťte.

1. Na této obrazovce vyberte **Načíst**.

   ![Vyberte možnost "Načíst"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Přejděte do souboru soukromého klíče ve formátu PuTTY a vyberte **Otevřít**.

1. V nabídce **Převody** vyberte **Exportovat klávesu OpenSSH**.

   ![Vyberte "Exportovat openssh klíč"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Uložte soubor soukromého `.pem` klíče s příponou názvu souboru.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Připojení k SFTP pomocí SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. U prázdných aplikací logiky zadejte `sftp ssh` do vyhledávacího pole jako filtr. V seznamu aktivačních událostí vyberte požadovanou aktivační událost.

   -nebo-

   U existujících aplikací logiky vyberte v posledním kroku, ve kterém chcete přidat akci, **nový krok**. Do vyhledávacího pole `sftp ssh` zadejte jako filtr. V seznamu akcí vyberte požadovanou akci.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Poskytněte potřebné podrobnosti pro připojení.

   > [!IMPORTANT]
   >
   > Když zadáte svůj soukromý klíč SSH ve vlastnosti **soukromého klíče SSH,** postupujte podle těchto dalších kroků, které vám pomohou zajistit, že pro tuto vlastnost zadáte úplnou a správnou hodnotu. Neplatný klíč způsobí selhání připojení.

   I když můžete použít libovolný textový editor, zde jsou ukázkové kroky, které ukazují, jak správně zkopírovat a vložit klíč pomocí poznámkového bloku.exe jako příklad.

   1. Otevřete soubor soukromého klíče SSH v textovém editoru. V tomto příkladu se používá poznámkový blok.

   1. V nabídce Poznámkový blok **Upravit** vyberte **Vybrat vše**.

   1. Vyberte **Upravit** > **kopii**.

   1. V aktivační události SFTP-SSH, kterou jste přidali, vložte *celý* klíč, který jste zkopírovali, do vlastnosti **soukromého klíče SSH,** která podporuje více řádků.  ***Ujistěte se, že jste vložili*** klíč. ***Nezadávejte ručně ani neupravujte klíč***.

1. Po zadání podrobností o připojení vyberte **Vytvořit**.

1. Teď poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Přepsat velikost bloku

Chcete-li přepsat výchozí adaptivní chování, které chunking používá, můžete určit konstantní velikost bloku bloku od 5 MB do 50 MB.

1. V pravém horním rohu akce vyberte tlačítko elipsy (**...**) a pak vyberte **Nastavení**.

   ![Otevření nastavení SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. V části **Přenos obsahu**zadejte do vlastnosti Velikost `5` `50` **bloku** hodnotu celéčíslo od do do , například: 

   ![Místo toho určete velikost bloku, který se má použít](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Jakmile budete hotovi, vyberte **Hotovo**.

## <a name="examples"></a>Příklady

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - Aktivační událost SSH: Při přidání nebo změně souboru

Tato aktivační událost spustí pracovní postup aplikace logiky při přidání nebo změně souboru na serveru SFTP. Můžete například přidat podmínku, která zkontroluje obsah souboru a získá obsah na základě toho, zda obsah splňuje zadanou podmínku. Potom můžete přidat akci, která získá obsah souboru a umístí tento obsah do složky na serveru SFTP.

**Příklad organizace**: Tuto aktivační událost můžete použít ke sledování složky SFTP pro nové soubory, které představují objednávky zákazníků. Potom můžete použít akci SFTP, jako je **například získat obsah souboru,** takže získáte obsah objednávky pro další zpracování a uložte tuto objednávku do databáze objednávek.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - Akce SSH: Získání obsahu pomocí cesty

Tato akce získá obsah ze souboru na serveru SFTP. Můžete například přidat aktivační událost z předchozího příkladu a podmínku, kterou musí splňovat obsah souboru. Pokud je podmínka pravdivá, akce, která získá obsah lze spustit.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/sftpwithssh/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
