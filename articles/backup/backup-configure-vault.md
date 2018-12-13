---
title: Zálohování souborů a složek pomocí agenta Azure Backup
description: Použijte agenta Microsoft Azure Backup k zálohování Windows souborů a složek do Azure. Vytvořte trezor služby Recovery Services, nainstalujte agenta zálohování, definovat zásady zálohování a spusťte prvotní zálohování u souborů a složek.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: Trezor záloh; zálohování Windows serveru. zálohování systému windows.
ms.service: backup
ms.topic: conceptual
ms.date: 8/5/2018
ms.author: raynew
ms.openlocfilehash: 4e138311b58d56f3a188347d43ed12287d43e1bd
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874001"
---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Zálohování klienta nebo Windows Serveru do Azure s využitím modelu nasazení Resource Manager
Tento článek vysvětluje, jak zálohovat Windows Server (nebo klienta Windows) souborů a složek do Azure pomocí Azure Backup pomocí modelu nasazení Resource Manager.

![Kroky procesu zálohování](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Než začnete
Zálohování klienta nebo serveru do Azure, potřebujete účet Azure. Pokud ho nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) během několika minut.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
Trezor služby Recovery Services je entita, která ukládá všechny zálohy a body obnovení, které vytvoříte v čase. Trezor služby Recovery Services obsahuje také zásadu zálohování, které jsou nastavené u chráněných souborů a složek. Když vytvoříte trezor služby Recovery Services, by měl také vybrat vhodnou zvolené možnosti redundance.

### <a name="to-create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
1. Pokud jste to ještě neudělali, přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí svého předplatného Azure.
2. V nabídce centra klikněte na **Všechny služby**, v seznamu prostředků zadejte **Recovery Services** a klikněte na **Trezory služby Recovery Services**.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Pokud předplatné obsahuje trezory služby Recovery Services, jsou tyto trezory uvedené v seznamu.

3. V nabídce **Trezory Recovery Services** klikněte na **Přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Otevře se okno trezoru Recovery Services s výzvou k vyplnění polí **Název**, **Předplatné**, **Skupina prostředků** a **Oblast**.

    ![Vytvoření trezoru Recovery Services – krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Jako **Název** zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný v rámci předplatného Azure. Zadejte název v rozsahu 2 až 50 znaků. Musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.

5. V části **Předplatné** z rozevírací nabídky vyberte předplatné Azure. Pokud používáte jenom jedno předplatné, zobrazí se toto předplatné a můžete přejít k dalšímu kroku. Pokud si nejste jisti, jaké předplatné použít, použijte výchozí (nebo navrhované) předplatné. Více možností je dostupných, jen pokud je váš účet organizace přidružený k více předplatným Azure.

6. V části **Skupina prostředků**:

    * vyberte **Vytvořit novou**, pokud chcete vytvořit novou skupinu prostředků.
    Nebo
    * vyberte **Použít existující** a kliknutím na rozevírací nabídku zobrazte seznam dostupných skupin prostředků.

  Kompletní informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).

7. Klikněte na **Oblast** a vyberte zeměpisnou oblast trezoru. Tato volba určuje geografickou oblast, kam jsou zasílaná vaše zálohovaná data.

8. V dolní části okna trezoru služby Recovery Services klikněte na **Vytvořit**.

  Vytvoření trezoru služby Recovery Services může trvat několik minut. Sledujte oznámení o stavu v pravé horní části portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Služeb zotavení. Pokud se trezor nezobrazí ani po několika minutách, klikněte na **Obnovit**.

  ![Kliknutí na tlačítko Obnovit](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

  Jakmile se trezor zobrazí v seznamu trezorů služby Recovery Services, jste připraveni nastavit redundanci úložiště.


### <a name="set-storage-redundancy"></a>Nastavení redundance úložiště
Při prvním vytvoření trezoru Služeb zotavení určíte, jak má být úložiště replikované.

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor.

    ![Výběr nového trezoru ze seznamu trezorů služby Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Po výběru trezoru se okno **Trezory služby Recovery Services** zúží a otevře se okno Nastavení (*s názvem trezoru v horní části*) a okno s podrobnostmi o trezoru.

    ![Zobrazení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)

2. V okně Nastavení nového trezoru pomocí vertikálního posuvníku přejděte dolů do části Správa a klikněte na **Infrastruktura zálohování**.

  Otevře se okno Infrastruktura zálohování.

3. V okně Infrastruktura zálohování klikněte na **Konfigurace zálohování**. Otevře se okno **Konfigurace zálohování**.

  ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)

4. Zvolte vhodnou možnost replikace pro svůj trezor.

  ![volby konfigurace úložiště](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

  Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Pokud používáte Azure jako primární koncový bod úložiště záloh, pokračujte v používání **geograficky redundantního** úložiště. Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště najdete v tomto [přehledu redundance úložiště](../storage/common/storage-redundancy.md).

Teď, když jste vytvořili trezor, připravíte infrastrukturu k zálohování souborů a složek stažením a instalací agenta Microsoft Azure Recovery Services, stahování přihlašovacích údajů trezoru a pak pomocí těchto přihlašovacích údajů k registraci agenta s trezor.

## <a name="configure-the-vault"></a>Konfigurace trezoru

1. V okně trezoru služby Recovery Services (pro trezor, který jste právě vytvořili) klikněte v části Začínáme na **Zálohovat** a potom v okně **Začínáme se zálohováním** vyberte **Cíl zálohování**.

  ![Otevřete okno cíle zálohování](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  Otevře se okno **Cíl zálohování**. Pokud do trezoru služby Recovery Services se už dříve nakonfigurovala, pak bude **cíl zálohování** oken se otevře po kliknutí na **zálohování** okně trezoru služeb zotavení.

  ![Otevřete okno cíle zálohování](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. V rozevírací nabídce **Kde běží vaše úlohy?** vyberte **Místní**.

  Možnost **Místní** jste vybrali proto, že počítačem s Windows Serverem nebo Windows je fyzický počítač, který není v Azure.

3. V nabídce **Co chcete zálohovat?** vyberte **Soubory a složky** a potom klikněte na **OK**.

  ![Konfigurace souborů a složek](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

  Po kliknutí na OK se vedle položky **Cíl zálohování** zobrazí zaškrtnutí a otevře se okno **Připravit infrastrukturu**.

  ![Cíl zálohování je nakonfigurovaný, teď se připraví infrastruktura](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. V okně **Připravit infrastrukturu** klikněte na **Stáhnout agenta pro Windows Server nebo klienta Windows**.

  ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

  Pokud používáte Windows Server Essential, vyberte ke stažení agenta pro Windows Server Essential. Místní nabídka zobrazí výzvu ke spuštění nebo uložení MARSAgentInstaller.exe.

  ![Dialogové okno MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. V místní nabídce stahování klikněte na **Uložit**.

  Ve výchozím nastavení se soubor **MARSagentinstaller.exe** uloží do složky Stažené soubory. Po dokončení instalačního programu se zobrazí automaticky otevírané okno s dotazem, jestli chcete spustit instalační program nebo otevřít složku.

  ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

  Agenta ještě nemusíte instalovat. Agenta můžete nainstalovat po stažení přihlašovacích údajů trezoru.

6. V okně **Připravit infrastrukturu** klikněte na **Stáhnout**.

  ![stažení přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

  Přihlašovací údaje trezoru se stáhnou do složky Stažené soubory. Po dokončení stahování přihlašovacích údajů trezoru se zobrazí automaticky otevírané okno s dotazem, jestli chcete přihlašovací údaje otevřít nebo uložit. Klikněte na **Uložit**. Pokud omylem kliknete **Otevřít**, nechte dialogové okno, které se pokusí otevřít přihlašovací údaje trezoru, zobrazit chybu. Přihlašovací údaje trezoru nejde otevřít. Přejděte k dalšímu kroku. Přihlašovací údaje trezoru jsou ve složce Stažené soubory.   

  ![dokončené stahování přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

> [!NOTE]
> Povolení zálohování prostřednictvím webu Azure Portal ještě není dostupné. K zálohování svých souborů a složek použijte agenta Microsoft Azure Recovery Services.
>

1. Ve složce Stažené soubory (nebo ve složce, kterou jste vybrali pro stahování) vyhledejte soubor **MARSagentinstaller.exe** a dvakrát na něj klikněte.

  Instalační program v průběhu extrakce, instalace a registrace agenta Recovery Services zobrazuje řadu zpráv.

  ![spuštění přihlašovacích údajů instalačního programu agenta Recovery Services](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Dokončete Průvodce instalací agenta Služeb zotavení Microsoft Azure. K dokončení průvodce budete muset:

  * Vybrat umístění instalace a složky mezipaměti.
  * Zadat informace o serveru proxy, pokud pro připojování k internetu používáte server proxy.
  * Zadat svoje uživatelské jméno a heslo, pokud používáte ověřený server proxy.
  * Zadat stažené přihlašovací údaje trezoru.
  * Uložit šifrovací heslo na bezpečné místo.

  > [!NOTE]
  > Pokud heslo ztratíte nebo zapomenete, Microsoft vám nemůže pomoci obnovit zálohovaná data. Uložte soubor na bezpečné místo. Je požadováno pro obnovení zálohy.
  >
  >

Agent je nyní nainstalovaný a váš počítač je registrovaný k trezoru. Jste připraveni nakonfigurovat a naplánovat zálohování.

## <a name="network-and-connectivity-requirements"></a>Požadavky na síť a připojení

Pokud váš počítač nebo proxy server má omezený přístup k Internetu, ujistěte se, že nastavení brány firewall na počítači a proxy umožňují následující adresy URL: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.net


## <a name="create-the-backup-policy"></a>Vytvoření zásady zálohování
Zásada zálohování je, naplánovat, kdy jsou pořizovány body obnovení a dobu, kterou se uchovají body obnovení. Vytvoření zásady zálohování souborů a složek pomocí agenta Microsoft Azure Backup.

### <a name="to-create-a-backup-schedule"></a>Vytvoření plánu zálohování
1. Otevřete agenta Microsoft Azure Backup. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.

    ![Spuštění agenta Azure Backup](./media/backup-configure-vault/snap-in-search.png)
2. V agentu Backup **akce** podokně klikněte na tlačítko **naplánovat zálohování** ke spuštění Průvodce plánem zálohování.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)

3. Na **Začínáme** stránky průvodce plánem zálohování klikněte na tlačítko **Další**.
4. Na **výběr položek k zálohování** klikněte na **přidat položky**.

  Otevře se dialogové okno Výběr položek.

5. Vyberte soubory a složky, které chcete chránit a potom klikněte na tlačítko **OK**.
6. V **výběr položek k zálohování** klikněte na **Další**.
7. Na **zadání plánu zálohování** zadejte plán zálohování a klikněte na tlačítko **Další**.

    Můžete naplánovat denní (probíhající maximálně třikrát za den) nebo týdenní zálohování.

    ![Položky k zálohování z Windows Serveru](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > Další informace o tom, jak zadat plán zálohování, naleznete v článku [Use Azure Backup to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md) (Použití služby Azure Backup k nahrazení páskové infrastruktury).
   >
   >

8. Na **výběr zásady uchovávání informací** zvolte zásady konkrétní uchovávání informací záložní kopie a klikněte na **Další**.

    Zásady uchovávání informací Určuje dobu, která je uložena záloha. Místo zadání „ploché zásady“ pro všechny body zálohy můžete zadat různé zásady uchovávání informací v závislosti na tom, kdy dochází k zálohování. Podle svých potřeb můžete upravit denní, týdenní, měsíční a roční zásady uchovávání informací.
9. Na stránce Výběr typu prvotní zálohy vyberte typ prvotní zálohy. Ponechejte vybranou možnost **Automaticky přes síť** a poté klikněte na **Další**.

    Zálohovat můžete automaticky přes síť nebo offline. Zbývající část tohoto článku popisuje proces automatického zálohování. Pokud upřednostňujete offline zálohování, přečtěte si článek [Pracovní postup offline zálohování v Azure Backup](backup-azure-backup-import-export.md), kde naleznete další informace.
10. Na stránce Potvrzení zkontrolujte informace a poté klikněte na **Dokončit**.
11. Až průvodce dokončí vytváření plánu zálohování, klikněte na **Zavřít**.

### <a name="enable-network-throttling"></a>Povolení omezení využití sítě
Agent Microsoft Azure Backup poskytuje, omezení šířky pásma sítě. Omezení využití šířky pásma sítě během přenosu dat ovládací prvky. Tento ovládací prvek může být užitečné, pokud potřebujete zálohovat data během pracovní době, ale nechcete, aby proces zálohování narušoval ostatní internetový provoz. Omezení šířky pásma se vztahuje na zálohování a obnovení činnosti.

> [!NOTE]
> Omezení šířky pásma sítě není k dispozici na Windows Server 2008 R2 SP1, Windows Server 2008 SP2 nebo Windows 7 (s aktualizací service Pack). Omezení funkcí sítě Azure Backup zaujme Quality of Service (QoS) místního operačního systému. I když Azure Backup může chránit tyto operační systémy, verzi k dispozici na těchto platformách kvality služby nefunguje s Azure Backup omezení sítě. Omezení využití sítě můžete použít na všechny ostatní [podporované operační systémy](backup-azure-backup-faq.md).
>
>

**K povolení omezení využití sítě**

1. V Microsoft Azure Backup agent, klikněte na tlačítko **změnit vlastnosti**.

    ![Změnit vlastnosti](./media/backup-configure-vault/change-properties.png)
2. Na **omezování** kartu, vyberte **Povolit omezování šířky pásma Internetu u operací zálohování** zaškrtávací políčko.

    ![Omezení využití sítě](./media/backup-configure-vault/throttling-dialog.png)
3. Po povolení omezení využití sítě, určení povolených šířky pásma pro přenos zálohovaných dat během **pracovních hodin** a **nepracovní hodiny**.

    Hodnoty šířky pásma začíná v hodnotě 512 kilobitů za sekundu (kb/s) a můžete přejít až 1,023 megabajtů (MB/s). Můžete také určit zahájení a dokončení **pracovních hodin**, a které dny v týdnu jsou považovány za pracovní dny. Hodiny mimo určené práci, které jsou považovány za hodiny mimo pracovní hodiny.
4. Klikněte na **OK**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>První zálohování souborů a složek
1. V agenta zálohování, klikněte na tlačítko **zálohovat nyní** dokončit prvotní synchronizaci přes síť.

    ![Zálohovat nyní ve Windows Serveru](./media/backup-configure-vault/backup-now.png)
2. Na stránce Potvrzení zkontrolujte nastavení, které Průvodce Zálohování nyní použije k zálohování počítače. Poté klikněte na **Zálohovat**.
3. Průvodce zavřete kliknutím na **Zavřít**. Pokud to uděláte před dokončením procesu zálohování, průvodce zůstane spuštěný v pozadí.

Po dokončení prvotní zálohy se v konzole Zálohování zobrazí stav **Úloha byla dokončena**.

![Dokončení IR](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>Máte dotazy?
Máte-li nějaké dotazy nebo pokud víte o funkci, kterou byste uvítali, [odešlete nám svůj názor](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Další postup
Další informace o zálohování virtuálních počítačů nebo jiné úlohy naleznete v tématu:

* Teď, když jste zálohovali své soubory a složky, můžete [spravovat svoje trezory a servery](backup-azure-manage-windows-server.md).
* Potřebujete-li obnovit zálohu, použijte tento článek k [obnovení souborů na počítač se systémem Windows](backup-azure-restore-windows-server.md).
