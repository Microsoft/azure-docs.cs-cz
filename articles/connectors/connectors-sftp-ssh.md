---
title: Připojení k serveru SFTP pomocí protokolu SSH
description: Automatizace úloh, které sledují, vytváří, spravují, odesílají a přijímají soubory pro server SFTP pomocí SSH a Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
tags: connectors
ms.openlocfilehash: cb851734dc8f71347168e7ac16ac0752845dda7b
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823618"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorování, vytváření a správa souborů SFTP pomocí SSH a Azure Logic Apps

Chcete-li automatizovat úlohy, které sledují, vytváří, odesílají a přijímaly soubory na serveru [Secure protokol FTP (File Transfer Protocol) (SFTP)](https://www.ssh.com/ssh/sftp/) pomocí protokolu [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) , můžete vytvářet a automatizovat integrační pracovní postupy pomocí Azure Logic Apps a konektoru SFTP-SSH. SFTP je síťový protokol, který poskytuje přístup k souborům, přenos souborů a správu souborů přes jakýkoliv spolehlivý datový proud.

> [!NOTE]
> Konektor SFTP-SSH v tuto chvíli nepodporuje tyto servery SFTP:
> 
> * IBM datapower
> * MessageWay
> * OpenText zabezpečená tabulka MFT
> * OpenText GXS

Tady je několik ukázkových úloh, které můžete automatizovat:

* Monitorování při přidání nebo změně souborů
* Získat, vytvořit, kopírovat, přejmenovat, aktualizovat, zobrazit seznam a odstranit soubory.
* Vytvořte složky.
* Získá obsah souboru a metadata.
* Extrahuje archivy do složek.

Můžete použít triggery, které sledují události na vašem serveru SFTP a zpřístupňují výstup ostatním akcím. Na serveru SFTP můžete použít akce, které provádějí různé úlohy. V aplikaci logiky můžete mít také jiné akce, které využívají výstup z akcí SFTP. Pokud například pravidelně načítáte soubory ze serveru SFTP, můžete odesílat e-mailové výstrahy týkající se těchto souborů a jejich obsahu pomocí konektoru Office 365 Outlook Connector nebo konektoru Outlook.com. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Rozdíly mezi konektorem SFTP-SSH a konektorem SFTP najdete v části [porovnání SFTP-SSH a SFTP](#comparison) dále v tomto tématu.

## <a name="limits"></a>Omezení

* Protokol SFTP – akce SSH, které podporují [dělení na bloky dat](../logic-apps/logic-apps-handle-large-messages.md) , můžou zpracovávat soubory o velikosti až 1 GB, zatímco akce SFTP-SSH, které nepodporují dělení na bloky dat, můžou zpracovávat soubory až do 50 MB. I když je výchozí velikost bloku 15 MB, může se tato velikost dynamicky měnit, počínaje 5 MB a postupně zvyšovat až 50 MB, a to na základě faktorů, jako je latence sítě, doba odezvy serveru a tak dále.

  > [!NOTE]
  > Pro aplikace logiky v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)vyžaduje ISE verze tohoto konektoru, aby místo toho používala [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

  Toto adaptivní chování můžete přepsat při [zadání velikosti konstantního bloku](#change-chunk-size) , která se má použít místo toho. Tato velikost může být v rozsahu od 5 MB do 50 MB. Předpokládejme například, že máte soubor 45 MB a síť, která může podporovat tuto velikost souboru bez latence. Adaptivní dělení výsledků v několika voláních, a ne v jednom volání. Pokud chcete snížit počet volání, můžete zkusit nastavit velikost bloku dat 50 MB. V různých scénářích platí, že pokud vaše aplikace logiky čeká na vypršení časového limitu, například při použití 15 MB bloků dat, můžete zkusit zmenšit velikost na 5 MB.

  Velikost bloku dat je přidružená k připojení, což znamená, že můžete použít stejné připojení pro akce, které podporují práci s bloky dat, a pak pro akce, které nepodporují práci s bloky dat. V takovém případě velikost bloku dat pro akce, které nepodporují rozsahy bloků dat z 5 MB na 50 MB. Tato tabulka ukazuje, které akce SFTP-SSH podporují vytváření bloků dat:

  | Akce | Podpora bloků dat | Přepsat podporu velikosti bloku |
  |--------|------------------|-----------------------------|
  | **Kopírovat soubor** | Ne | Nelze použít |
  | **Vytvořit soubor** | Ano | Ano |
  | **Vytvořit složku** | Není | Není |
  | **Odstranit dlaždici** | Není | Není |
  | **Extrakce archivu do složky** | Není | Není |
  | **Získat obsah souboru** | Ano | Ano |
  | **Získání obsahu souboru pomocí cesty** | Ano | Ano |
  | **Získat metadata souboru** | Není | Není |
  | **Získat metadata souboru pomocí cesty** | Není | Není |
  | **Zobrazit seznam souborů ve složce** | Není | Není |
  | **Přejmenovat soubor** | Není | Není |
  | **Aktualizovat soubor** | Ne | Nelze použít |
  ||||

* Protokol SFTP – triggery SSH nepodporují bloky zpráv. Při vyžádání obsahu souboru triggery vyberou pouze soubory, které jsou 15 MB nebo menší. Pokud chcete získat soubory větší než 15 MB, použijte tento vzor:

  1. Použijte Trigger SFTP-SSH, který vrátí pouze vlastnosti souboru, například **při přidání nebo úpravě souboru (pouze vlastnosti)**.

  1. Postupujte podle aktivační události s protokolem SFTP-SSH **získat obsah souboru** , který načte kompletní soubor a implicitně použije bloky zpráv.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Porovnání protokolu SFTP – SSH versus SFTP

Tady jsou další klíčové rozdíly mezi konektorem SFTP-SSH a konektorem SFTP, kde má konektor SFTP-SSH tyto možnosti:

* Používá [knihovnu SSH.NET](https://github.com/sshnet/SSH.NET), což je open-source Secure Shell (SSH) Library podporující rozhraní .NET.

* Poskytuje akci **vytvořit složku** , která vytvoří složku v zadané cestě na serveru SFTP.

* Poskytuje akci **Přejmenovat soubor** , která přejmenuje soubor na serveru SFTP.

* Uloží připojení do serveru SFTP *po dobu až 1 hodiny*, což zvyšuje výkon a snižuje počet pokusů o připojení k serveru. Pokud chcete nastavit dobu trvání tohoto chování při ukládání do mezipaměti, upravte vlastnost [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) v konfiguraci SSH na vašem serveru SFTP.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Vaše adresa serveru SFTP a přihlašovací údaje účtu, které vaší aplikaci logiky umožní přístup k vašemu účtu SFTP. Potřebujete také přístup k privátnímu klíči SSH a k heslu privátního klíče SSH. Pokud chcete při nahrávání velkých souborů použít bloky dat, potřebujete oprávnění ke čtení i zápisu pro kořenovou složku na vašem serveru SFTP. V opačném případě se zobrazí chyba "401 neautorizované".

  > [!IMPORTANT]
  >
  > Konektor SFTP-SSH podporuje *pouze* tyto formáty privátních klíčů, algoritmy a otisky prstů:
  >
  > * **Formáty privátních klíčů**: klíče RSA (Rivest Shamir Adleman) a DSA (Digital Signature Algorithm) ve formátech OpenSSH a SSH.com. Pokud je váš privátní klíč ve formátu výstupního souboru (. ppk), nejprve [převeďte klíč na formát souboru OpenSSH (. pem)](#convert-to-openssh).
  >
  > * **Algoritmy šifrování**: des-EDE3-CBC, des-EDE3-CFB, des-CBC, AES-128-CBC, AES-192-CBC a AES-256-CBC
  >
  > * **Otisk prstu**: MD5
  >
  > Po přidání triggeru SFTP-SSH nebo akce, kterou chcete použít pro vaši aplikaci logiky, je nutné zadat informace o připojení pro váš server SFTP. Když pro toto připojení zadáte privátní klíč SSH, ***nemusíte ručně zadávat ani upravovat klíč***, což by mohlo způsobit selhání připojení. Místo toho nezapomeňte ***zkopírovat klíč*** ze souboru privátního klíče SSH a ***Vložit*** tento klíč do podrobností o připojení. 
  > Další informace najdete v části [připojení k SFTP s](#connect) protokolem SSH dále v tomto článku.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k vašemu účtu SFTP. Pokud chcete začít s triggerem SFTP-SSH, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci SFTP-SSH, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="how-sftp-ssh-triggers-work"></a>Princip fungování aktivačních událostí protokolu SFTP-SSH

SFTP – SSH spouští dotazování systému souborů SFTP a hledání všech souborů, které se od posledního cyklického dotazování změnily. Některé nástroje umožňují zachovat časové razítko při změně souborů. V těchto případech je nutné zakázat tuto funkci, aby mohla Trigger fungovat. Tady je několik běžných nastavení:

| Klient SFTP | Akce |
|-------------|--------|
| WinSCP | Přejít na **Možnosti**  >  **Předvolby**  >  **přenos**  >  **Upravit**  >  **zachovat časové razítko**  >  **Zakázat** |
| FileZilly | Přejít na **přenos**–  >  zachovat zablokovaná**Časová razítka přenesených souborů**  >  **Disable** |
|||

Pokud aktivační událost najde nový soubor, aktivační událost zkontroluje, jestli je nový soubor hotový, a ne částečně napsaný. Soubor může mít například probíhající změny, když aktivační událost kontroluje souborový server. Aby nedošlo k vrácení částečně napsaného souboru, aktivační událost zapisuje časové razítko pro soubor, který má poslední změny, ale tento soubor okamžitě nevrátí. Aktivační událost vrátí soubor pouze při opakovaném dotazování serveru. V některých případech může toto chování způsobit zpoždění až dvojnásobku intervalu dotazování triggeru.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Převést klíč založený na výstupu na OpenSSH

Pokud je váš privátní klíč ve formátu výstupního souboru, který používá příponu názvu souboru. ppk (zakládá se do poštovního formátu), nejprve převeďte klíč na formát OpenSSH, který používá příponu názvu souboru. pem (ochrana osobních údajů).

### <a name="unix-based-os"></a>OPERAČNÍ systém UNIX

1. Pokud v systému ještě nejsou nainstalované nástroje pro výstupy, udělejte to třeba takto:

   `sudo apt-get install -y putty`

1. Spusťte tento příkaz, který vytvoří soubor, který můžete použít s konektorem SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Příklad:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Operační systém Windows

1. Pokud jste to ještě neudělali, [Stáhněte si nejnovější nástroj pro generátory výstupu (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)a pak nástroj spusťte.

1. Na této obrazovce vyberte **načíst**.

   ![Vyberte načíst.](./media/connectors-sftp-ssh/puttygen-load.png)

1. Přejděte k souboru privátního klíče ve formátu RTF a vyberte **otevřít**.

1. V nabídce **převody** vyberte **exportovat OpenSSH klíč**.

   ![Vyberte Exportovat OpenSSH klíč.](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Uložte soubor privátního klíče s `.pem` příponou názvu souboru.

## <a name="considerations"></a>Požadavky

Tato část popisuje pokyny ke kontrole triggerů a akcí tohoto konektoru.

<a name="create-file"></a>

### <a name="create-file"></a>Vytvořit soubor

Pokud chcete vytvořit soubor na vašem serveru SFTP, můžete použít akci SFTP-SSH **vytvořit soubor** . Když tato akce vytvoří soubor, služba Logic Apps taky automaticky zavolá váš server SFTP, aby získal metadata souboru. Pokud však přesunete nově vytvořený soubor předtím, než služba Logic Apps umožní volání získat metadata, zobrazí se `404` chybová zpráva `'A reference was made to a file or folder which does not exist'` . Pokud chcete přeskočit čtení metadat souboru po vytvoření souboru, postupujte podle pokynů pro [Přidání a nastavení vlastnosti **načíst všechny souborové metadata** na **ne**](#file-does-not-exist).

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Připojení k SFTP pomocí SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné aplikace logiky zadejte do vyhledávacího pole `sftp ssh` jako filtr. V seznamu triggery vyberte aktivační událost, kterou chcete.

   -nebo-

   Pro existující aplikace logiky v rámci posledního kroku, kam chcete přidat akci, vyberte **Nový krok**. Do vyhledávacího pole zadejte `sftp ssh` jako filtr. V seznamu akce vyberte akci, kterou chcete.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Zadejte potřebné informace pro vaše připojení.

   > [!IMPORTANT]
   >
   > Když zadáte privátní klíč SSH do vlastnosti **privátního klíče SSH** , postupujte podle těchto dalších kroků, které vám pomůžou zajistit, aby byla pro tuto vlastnost k dispozici úplná a správná hodnota. Neplatný klíč způsobí selhání připojení.

   I když můžete použít libovolný textový editor, tady je ukázkový postup, který ukazuje, jak správně zkopírovat a vložit klíč pomocí Notepad.exe jako příklad.

   1. V textovém editoru otevřete soubor privátního klíče SSH. Tyto kroky používají jako příklad program Poznámkový blok.

   1. V nabídce **Úpravy** poznámkového bloku vyberte **Vybrat vše**.

   1. Vyberte **Upravit**  >  **kopii**.

   1. V aktivační události SFTP-SSH nebo v akci, kterou jste přidali, vložte *úplný* klíč, který jste zkopírovali do vlastnosti **privátního klíče SSH** , který podporuje více řádků.  ***Nezapomeňte klíč vložit*** . ***Klíč nezadejte ručně ani neupravujte***.

1. Až skončíte s zadáním podrobností o připojení, vyberte **vytvořit**.

1. Teď zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Přepsat velikost bloku dat

Chcete-li přepsat výchozí adaptivní chování, které využívá bloky dat, můžete zadat konstantní velikost bloku od 5 MB do 50 MB.

1. V pravém horním rohu akce vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.

   ![Otevření nastavení SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. V části **přenos obsahu**zadejte do vlastnosti **velikost bloku** Integer hodnotu `5` `50` , například: 

   ![Místo toho zadejte velikost bloku, která se má použít.](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Jakmile budete hotovi, vyberte **Hotovo**.

## <a name="examples"></a>Příklady

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – Trigger SSH: při přidání nebo úpravě souboru

Tato aktivační událost spustí pracovní postup aplikace logiky při přidání nebo změně souboru na serveru SFTP. Můžete například přidat podmínku, která zkontroluje obsah souboru a získá obsah na základě toho, jestli obsah splňuje zadanou podmínku. Pak můžete přidat akci, která získá obsah souboru, a tento obsah vložit do složky na serveru SFTP.

**Podnikový příklad**: tuto aktivační událost můžete použít k monitorování složky SFTP pro nové soubory, které reprezentují objednávky zákazníků. Pak můžete použít akci SFTP, například **získat obsah souboru** , abyste získali obsah objednávky pro další zpracování a uložení tohoto pořadí v databázi objednávek.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>Protokol SFTP – akce SSH: získání obsahu souboru pomocí cesty

Tato akce načte obsah ze souboru na serveru SFTP zadáním cesty k souboru. Například můžete přidat Trigger z předchozího příkladu a podmínku, že obsah souboru musí splňovat. Pokud je podmínka pravdivá, bude možné spustit akci, která načte obsah.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-errors"></a>Řešení chyb

Tato část popisuje možná řešení běžných chyb a problémů.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 chyba: "odkaz byl proveden na soubor nebo složku, která neexistuje"

K této chybě může dojít, když aplikace logiky vytvoří nový soubor na serveru SFTP prostřednictvím akce **Vytvoření souboru** protokolu SFTP-SSH, ale nově vytvořený soubor se ihned přesune ještě předtím, než služba Logic Apps může získat metadata souboru. Když aplikace logiky spustí akci **vytvořit soubor** , služba Logic Apps taky automaticky zavolá váš server SFTP, aby získal metadata souboru. Pokud se ale soubor přesune, služba Logic Apps už nebude soubor najít, takže se zobrazí `404` chybová zpráva.

Pokud se soubor nemůžete vyhnout nebo chcete-li ho odložit, můžete přeskočit čtení metadat souboru za vytvořením souboru pomocí následujících kroků:

1. V akci **vytvořit soubor** otevřete seznam **Přidat nový parametr** , vyberte vlastnost **získat všechny souborové metadata** a nastavte hodnotu na **ne**.

1. Pokud tato metadata souboru budete potřebovat později, můžete použít akci **získat metadata souboru** .

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 chyba: pokus o připojení se nezdařil, protože připojená strana nereagovala správně po uplynutí určité doby nebo navázáno připojení selhalo, protože připojení hostitele nedokázalo reagovat nebo požadavek na server SFTP trval více než 00:00:30 sekund.

K této chybě může dojít, když aplikace logiky nemůže úspěšně navázat spojení se serverem SFTP. Může existovat řada různých důvodů a doporučujeme problém vyřešit z následujících aspektů. 

1. Časový limit připojení je 20 sekund. Ujistěte se prosím, že server SFTP má dobrý výkon a že zařízení intermidiate, jako je brána firewall, nezvyšuje spoustu režie. 

2. Pokud je brána firewall zapojená, ujistěte se, že se do seznamu schválených přidaly **IP adresy spravovaného konektoru** . Tyto IP adresy můžete najít v oblasti aplikace logiky [**zde**] (https://docs.microsoft.com/azure/logic-apps/logic-apps-limits-and-config#multi-tenant-azure---outbound-ip-addresses)

3. Pokud se jedná o občasný problém, otestujte prosím nastavení opakování, abyste viděli, jestli vám může pomáhat vyšší počet opakování, než je výchozí 4.

4. Zkontrolujte prosím, jestli server SFTP omezuje počet připojení z každé IP adresy. Pokud ano, možná budete muset omezit počet souběžných instancí aplikace logiky. 

5. Zvýšením vlastnosti [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na serveru SFTP na 1 hodinu v konfiguraci SSH snížíte náklady na připojení.

6. Můžete ověřit protokol serveru SFTP a zjistit, zda požadavek z aplikace logiky byl někdy dosažen se serverem SFTP. V bráně firewall a vašem serveru SFTP můžete také sledovat trasování sítě, abyste se mohli dig k problémům s připojením.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/sftpwithssh/).

> [!NOTE]
> Pro Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-labeling vyžaduje, aby místo toho používala [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
