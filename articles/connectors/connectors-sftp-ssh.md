---
title: Připojení k serveru SFTP pomocí protokolu SSH
description: Automatizace úloh, které sledují, vytváří, spravují, odesílají a přijímají soubory pro server SFTP pomocí SSH a Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 03/08/2021
tags: connectors
ms.openlocfilehash: 983e0d34692d67302e11c35abac590fefd610b2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449624"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorování, vytváření a správa souborů SFTP pomocí SSH a Azure Logic Apps

Chcete-li automatizovat úlohy, které sledují, vytváří, odesílají a přijímaly soubory na serveru [Secure protokol FTP (File Transfer Protocol) (SFTP)](https://www.ssh.com/ssh/sftp/) pomocí protokolu [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) , můžete vytvářet a automatizovat integrační pracovní postupy pomocí Azure Logic Apps a konektoru SFTP-SSH. SFTP je síťový protokol, který poskytuje přístup k souborům, přenos souborů a správu souborů přes jakýkoliv spolehlivý datový proud.

Tady je několik ukázkových úloh, které můžete automatizovat:

* Monitorování při přidání nebo změně souborů
* Získat, vytvořit, kopírovat, přejmenovat, aktualizovat, zobrazit seznam a odstranit soubory.
* Vytvořte složky.
* Získá obsah souboru a metadata.
* Extrahuje archivy do složek.

Můžete použít triggery, které sledují události na vašem serveru SFTP a zpřístupňují výstup ostatním akcím. Na serveru SFTP můžete použít akce, které provádějí různé úlohy. V aplikaci logiky můžete mít také jiné akce, které využívají výstup z akcí SFTP. Pokud například pravidelně načítáte soubory ze serveru SFTP, můžete odesílat e-mailové výstrahy týkající se těchto souborů a jejich obsahu pomocí konektoru Office 365 Outlook Connector nebo konektoru Outlook.com. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Rozdíly mezi konektorem SFTP-SSH a konektorem SFTP najdete v části [porovnání SFTP-SSH a SFTP](#comparison) dále v tomto tématu.

## <a name="limits"></a>Omezení

* Konektor SFTP-SSH v tuto chvíli nepodporuje tyto servery SFTP:

  * IBM datapower
  * MessageWay
  * OpenText zabezpečená tabulka MFT
  * OpenText GXS

* Konektor SFTP-SSH podporuje buď ověřování privátního klíče, nebo ověřování hesla, nikoli obojí.

* Protokol SFTP – akce SSH, které podporují [dělení na bloky dat](../logic-apps/logic-apps-handle-large-messages.md) , můžou zpracovávat soubory o velikosti až 1 GB, zatímco akce SFTP-SSH, které nepodporují dělení na bloky dat, můžou zpracovávat soubory až do 50 MB. I když je výchozí velikost bloku 15 MB, tato velikost se může dynamicky měnit, počínaje 5 MB a postupně zvyšovat až 50 MB, a to na základě faktorů, jako je latence sítě, doba odezvy serveru a tak dále.

  > [!NOTE]
  > Pro aplikace logiky v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)vyžaduje ISE verze tohoto konektoru, aby místo toho používala [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

  Toto adaptivní chování můžete přepsat při [zadání velikosti konstantního bloku](#change-chunk-size) , která se má použít místo toho. Tato velikost může být v rozsahu od 5 MB do 50 MB. Předpokládejme například, že máte soubor 45 MB a síť, která může podporovat tuto velikost souboru bez latence. Adaptivní dělení výsledků v několika voláních, a ne v jednom volání. Pokud chcete snížit počet volání, můžete zkusit nastavit velikost bloku 50 MB. V různých scénářích platí, že pokud vaše aplikace logiky čeká na vypršení časového limitu, například při použití bloků dat na 15 MB, můžete zkusit zmenšit velikost na 5 MB.

  Velikost bloku dat je přidružená k připojení, což znamená, že můžete použít stejné připojení pro akce, které podporují práci s bloky dat, a pak pro akce, které nepodporují práci s bloky dat. V takovém případě velikost bloku dat pro akce, které nepodporují rozsahy bloků dat z 5 MB na 50 MB. Tato tabulka ukazuje, které akce SFTP-SSH podporují vytváření bloků dat:

  | Akce | Podpora bloků dat | Přepsat podporu velikosti bloku |
  |--------|------------------|-----------------------------|
  | **Kopírovat soubor** | Ne | Nelze použít |
  | **Vytvořit soubor** | Yes | Yes |
  | **Vytvořit složku** | Nelze použít | Nelze použít |
  | **Odstranit soubor** | Nelze použít | Nelze použít |
  | **Extrakce archivu do složky** | Nelze použít | Nelze použít |
  | **Získat obsah souboru** | Yes | Yes |
  | **Získání obsahu souboru pomocí cesty** | Yes | Yes |
  | **Získat metadata souboru** | Nelze použít | Nelze použít |
  | **Získat metadata souboru pomocí cesty** | Nelze použít | Nelze použít |
  | **Zobrazit seznam souborů ve složce** | Nelze použít | Nelze použít |
  | **Přejmenovat soubor** | Nelze použít | Nelze použít |
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

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Chování cyklického dotazování

SFTP – SSH spustí dotaz do systému souborů SFTP a vyhledá všechny soubory, které se od posledního cyklického dotazování změnily. Některé nástroje umožňují zachovat časové razítko při změně souborů. V těchto případech je nutné zakázat tuto funkci, aby mohla Trigger fungovat. Tady je několik běžných nastavení:

| Klient SFTP | Akce |
|-------------|--------|
| WinSCP | Přejít na **Možnosti**  >  **Předvolby**  >  **přenos**  >  **Upravit**  >  **zachovat časové razítko**  >  **Zakázat** |
| FileZilly | Přejít na **přenos**–  >  zachovat zablokovaná **Časová razítka přenesených souborů**  >   |
|||

Pokud aktivační událost najde nový soubor, aktivační událost zkontroluje, jestli je nový soubor hotový, a ne částečně napsaný. Soubor může mít například probíhající změny, když aktivační událost kontroluje souborový server. Aby nedošlo k vrácení částečně napsaného souboru, aktivační událost zapisuje časové razítko pro soubor, který má poslední změny, ale tento soubor okamžitě nevrátí. Aktivační událost vrátí soubor pouze při opakovaném dotazování serveru. V některých případech může toto chování způsobit zpoždění až dvojnásobku intervalu dotazování triggeru.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Aktivovat opakování a posunování

Aktivační události na základě připojení, kde je třeba vytvořit připojení jako první, jako je například Trigger SFTP-SSH, se liší od integrovaných triggerů, které se spouštějí nativně v Azure Logic Apps, jako je například [Trigger opakování](../connectors/connectors-native-recurrence.md). V případě opakovaných triggerů založených na připojení není plán opakování jediným ovladačem, který řídí provádění, a časové pásmo určuje pouze počáteční čas spuštění. Následná spuštění závisí na plánu opakování, posledním spuštění triggeru *a* dalších faktorech, které by mohly způsobit snížení nebo zpracování neočekávaného chování, například bez zachování zadaného plánu při zahájení a ukončení letního času (DST). Chcete-li se ujistit, že se doba opakování nemění, když se letní čas projeví, proveďte ruční úpravu opakování, aby vaše aplikace logiky běžela v očekávaném čase. V opačném případě se čas zahájení posune jednu hodinu dopředu při zahájení LETNÍho času a hodinu zpětně od konce LETNÍho času. Další informace najdete v tématu [opakování pro aktivační události na základě připojení](../connectors/apis-list.md#recurrence-connection-based).

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Převést klíč založený na výstupu na OpenSSH

Pokud je váš privátní klíč ve formátu výstupního souboru, který používá příponu názvu souboru. ppk (zakládá se do poštovního formátu), nejprve převeďte klíč na formát OpenSSH, který používá příponu názvu souboru. pem (ochrana osobních údajů).

### <a name="unix-based-os"></a>OPERAČNÍ systém UNIX

1. Pokud v systému nemáte nainstalované nástroje pro výstupy, udělejte to teď například takto:

   `sudo apt-get install -y putty`

1. Spusťte tento příkaz, který vytvoří soubor, který můžete použít s konektorem SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Například:

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

V této části jsou popsány požadavky na kontrolu při použití triggerů a akcí tohoto konektoru.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Pro nahrávání a zpracování souborů použít jiné složky protokolu SFTP

Na serveru SFTP se ujistěte, že používáte samostatné složky, kam ukládáte nahrané soubory a kde Trigger monitoruje tyto soubory ke zpracování, což znamená, že potřebujete způsob, jak přesouvat soubory mezi těmito složkami. V opačném případě se Trigger neaktivuje a nebude se chovat nepředvídatelné, například přeskočení náhodného počtu souborů, které aktivační událost zpracovává.

Pokud k tomuto problému dojde, odeberte soubory ze složky, kterou aktivační událost monitoruje, a k uložení nahraných souborů použijte jinou složku.

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

   1. V aktivační události SFTP-SSH nebo v akci, kterou jste přidali, vložte *úplný* klíč, který jste zkopírovali do vlastnosti **privátního klíče SSH** , který podporuje více řádků.  Ujistěte se, **_že jste vložili_*klíč _. _* tento _klíč nemusíte zadávat ani upravovat ručně_**.

1. Po dokončení zadávání podrobností o připojení vyberte **vytvořit**.

1. Teď zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Přepsat velikost bloku dat

Chcete-li přepsat výchozí adaptivní chování, které využívá bloky dat, můžete zadat konstantní velikost bloku od 5 MB do 50 MB.

1. V pravém horním rohu akce vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.

   ![Otevření nastavení SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. V části **přenos obsahu** zadejte do vlastnosti **velikost bloku** Integer hodnotu `5` `50` , například: 

   ![Místo toho zadejte velikost bloku, která se má použít.](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Po dokončení vyberte **Hotovo**.

## <a name="examples"></a>Příklady

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – Trigger SSH: při přidání nebo úpravě souboru

Tato aktivační událost spustí pracovní postup aplikace logiky při přidání nebo změně souboru na serveru SFTP. Můžete například přidat podmínku, která zkontroluje obsah souboru a získá obsah na základě toho, jestli obsah splňuje zadanou podmínku. Pak můžete přidat akci, která získá obsah souboru, a tento obsah vložit do složky na serveru SFTP.

**Podnikový příklad**: tuto aktivační událost můžete použít k monitorování složky SFTP pro nové soubory, které reprezentují objednávky zákazníků. Pak můžete použít akci SFTP, například **získat obsah souboru** , abyste získali obsah objednávky pro další zpracování a uložení tohoto pořadí v databázi objednávek.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>Protokol SFTP – akce SSH: získání obsahu souboru pomocí cesty

Tato akce načte obsah ze souboru na serveru SFTP zadáním cesty k souboru. Například můžete přidat Trigger z předchozího příkladu a podmínku, že obsah souboru musí splňovat. Pokud je podmínka pravdivá, bude možné spustit akci, která načte obsah.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Poradce při potížích

Tato část popisuje možná řešení běžných chyb a problémů.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 chyba: pokus o připojení se nezdařil, protože připojená strana nereagovala správně po uplynutí určité doby nebo navázáno připojení selhalo, protože připojení hostitele nedokázalo reagovat nebo požadavek na server SFTP trval více než 00:00:30 sekund.

K této chybě může dojít, když aplikace logiky nemůže úspěšně navázat spojení se serverem SFTP. K tomuto problému může dojít z různých důvodů, proto zkuste tyto možnosti řešení potíží:

* Časový limit připojení je 20 sekund. Ověřte, že váš server SFTP má dobrý výkon a středně pokročilá zařízení, jako jsou brány firewall, nepřidává režii. 

* Pokud máte nastavenou bránu firewall, nezapomeňte přidat **IP adresy spravovaného konektoru** do seznamu schválených. Pokud chcete najít IP adresy pro oblast vaší aplikace logiky, přečtěte si téma [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses).

* Pokud k této chybě dochází občas, změňte nastavení **zásad opakování** u akce SFTP-SSH na počet opakovaných pokusů, který je vyšší než výchozí čtyři pokusy.

* Ověřte, zda server SFTP omezuje počet připojení z každé IP adresy. Pokud existuje limit, možná budete muset omezit počet souběžných instancí aplikace logiky.

* Chcete-li snížit náklady na připojení, zvyšte v konfiguraci SSH pro váš server SFTP vlastnost [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na přibližně jednu hodinu.

* Zkontrolujte protokol serveru SFTP a zkontrolujte, zda požadavek z aplikace logiky dosáhl serveru SFTP. Pokud chcete získat další informace o problému s připojením, můžete také spustit síťové trasování na bráně firewall a serveru SFTP.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 chyba: "odkaz byl proveden na soubor nebo složku, která neexistuje"

K této chybě může dojít, když aplikace logiky vytvoří nový soubor na serveru SFTP prostřednictvím akce SFTP-SSH **Create File** , ale okamžitě přesune nově vytvořený soubor, aby mohla služba Logic Apps získat metadata souboru. Když aplikace logiky spustí akci **vytvořit soubor** , služba Logic Apps taky automaticky zavolá váš server SFTP, aby získal metadata souboru. Pokud však vaše aplikace logiky přesune soubor, služba Logic Apps již nemůže najít soubor, takže se zobrazí `404` chybová zpráva.

Pokud se soubor nemůžete vyhnout nebo chcete-li ho odložit, můžete přeskočit čtení metadat souboru za vytvořením souboru pomocí následujících kroků:

1. V akci **vytvořit soubor** otevřete seznam **Přidat nový parametr** , vyberte vlastnost **získat všechny souborové metadata** a nastavte hodnotu na **ne**.

1. Pokud tato metadata souboru budete potřebovat později, můžete použít akci **získat metadata souboru** .

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/sftpwithssh/).

> [!NOTE]
> Pro Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-labeling vyžaduje, aby místo toho používala [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
