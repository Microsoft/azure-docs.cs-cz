---
title: Začínáme s Azure Log Integration | Microsoft Docs
description: Naučte se instalovat službu Azure Log Integration a integrovat protokoly z Azure Storage, protokoly auditování Azure a Azure Security Center výstrahy.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: d88df6fe7a4ef36fb066bac880f2c4a393a8639d
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614040"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration pomocí protokolování Azure Diagnostics a předávání událostí systému Windows


>[!IMPORTANT]
> Funkce integrace protokolu Azure bude zastaralá od 06/15/2019. Stahování AzLog bylo zakázáno 27. června 2018. Další informace o tom, co dělat, najdete v příspěvku [pomocí Azure monitoru pro integraci s nástroji Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) . 

Integraci protokolů Azure byste měli používat jenom v případě, že konektor [Azure monitor](/azure/monitoring-and-diagnostics/monitoring-get-started) není dostupný od dodavatele zabezpečení a Siem (Event Management Security).

Azure Log Integration zpřístupňuje protokoly Azure vašim SIEM, abyste mohli vytvořit jednotný řídicí panel zabezpečení pro všechny vaše prostředky.
Pokud chcete získat další informace o stavu konektoru Azure Monitor, obraťte se na dodavatele SIEM.

> [!IMPORTANT]
> Pokud váš primární zájem shromažďuje protokoly virtuálních počítačů, většina dodavatelů SIEM zahrnuje tuto možnost do svého řešení. Použití konektoru dodavatele SIEM je vždy upřednostňovanou alternativou.

Tento článek vám pomůže začít s Azure Log Integration. Zaměřuje se na instalaci služby Azure Log Integration a integraci služby s Azure Diagnostics. Služba Azure Log Integration pak shromáždí informace protokolu událostí Windows z kanálu událostí zabezpečení systému Windows z virtuálních počítačů nasazených v infrastruktuře Azure jako služba. To se podobá *předávání událostí* , které můžete použít v místním systému.

> [!NOTE]
> Integrování výstupu Azure Log Integration s SIEM se provádí samotným SIEM. Další informace najdete v tématu věnovaném [integraci Azure log Integration s místními Siem](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Služba Azure Log Integration běží na fyzickém nebo virtuálním počítači se systémem Windows Server 2008 R2 nebo novějším (upřednostňuje se Windows Server 2016 nebo Windows Server 2012 R2).

Fyzický počítač může běžet místně nebo na hostitelském serveru. Pokud se rozhodnete spustit službu Azure Log Integration na virtuálním počítači, může se virtuální počítač nacházet místně nebo ve veřejném cloudu, například v Microsoft Azure.

Fyzický nebo virtuální počítač, na kterém běží služba Azure Log Integration, vyžaduje síťové připojení ke veřejnému cloudu Azure. Tento článek poskytuje podrobné informace o požadované konfiguraci.

## <a name="prerequisites"></a>Požadavky

Minimálně instalace Azure Log Integration vyžaduje následující položky:

* **Předplatného Azure**. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* **Účet úložiště** , který se dá použít pro protokolování Windows Azure Diagnostics (WAD). Můžete použít předkonfigurovaný účet úložiště nebo vytvořit nový účet úložiště. Později v tomto článku popisujeme, jak nakonfigurovat účet úložiště.

  > [!NOTE]
  > V závislosti na vašem scénáři nemusí být účet úložiště nutný. Pro Azure Diagnostics scénář, který je popsaný v tomto článku, je vyžadován účet úložiště.

* **Dva systémy**: 
  * Počítač, ve kterém je spuštěna služba Azure Log Integration. Tento počítač shromažďuje všechny informace protokolu, které později importuje do svého SIEMu. Tento systém:
    * Může být místní nebo hostovaná v Microsoft Azure.  
    * Musí běžet verze x64 systému Windows Server 2008 R2 SP1 nebo novější a musí mít nainstalované rozhraní Microsoft .NET 4.5.1. Pokud chcete zjistit nainstalovanou verzi rozhraní .NET, přečtěte si téma [určení, které verze .NET Framework jsou nainstalované](https://msdn.microsoft.com/library/hh925568).  
    * Musí mít připojení k účtu Azure Storage, který se používá pro Azure Diagnostics protokolování. Později v tomto článku popisujeme, jak potvrdit připojení.
  * Počítač, který chcete monitorovat. Toto je virtuální počítač, který běží jako [virtuální počítač Azure](/azure/virtual-machines/virtual-machines-windows-overview). Informace o protokolování z tohoto počítače se odesílají do počítače služby Azure Log Integration.

Chcete-li získat rychlou ukázku, jak vytvořit virtuální počítač pomocí Azure Portal, podívejte se na následující video:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Aspekty nasazování

Během testování můžete použít libovolný systém, který splňuje minimální požadavky na operační systém. V produkčním prostředí může zatížení vyžadovat, abyste naplánovali horizontální navýšení nebo škálování.

Můžete spustit více instancí služby Azure Log Integration. Můžete ale spustit jenom jednu instanci služby na fyzický nebo virtuální počítač. Kromě toho můžete vyrovnávat zatížení Azure Diagnostics účtů úložiště pro WAD. Počet předplatných, která se mají poskytnout pro tyto instance, je založený na vaší kapacitě.

> [!NOTE]
> V současné době nemáme specifická doporučení týkající se horizontálního navýšení kapacity instancí Azure Log Integration počítačů (tj. počítačů se spuštěnou službou Azure Log Integration) nebo pro účty úložiště nebo odběry. Zajistěte rozhodování o škálování na základě pozorování výkonu v každé z těchto oblastí.

Pro zvýšení výkonu můžete také využít možnost horizontálního navýšení kapacity služby Azure Log Integration. Následující metriky výkonu vám pomůžou určit velikost počítačů, u kterých se rozhodnete spustit službu Azure Log Integration:

* V počítači s 8 procesory může jediná instance Azure Log Integration zpracovat události 24 000 000 za den (přibližně 1 000 000 událostí za hodinu).
* V počítači se 4 procesory může jediná instance Azure Log Integration zpracovat události 1 500 000 za den (přibližně 62 500 událostí za hodinu).

## <a name="install-azure-log-integration"></a>Nainstalovat Azure Log Integration

Spusťte rutinu Set up. Určete, zda chcete společnosti Microsoft poskytnout informace o telemetrie.

Služba Azure Log Integration shromažďuje data telemetrie z počítače, na kterém je nainstalovaná.  

Shromážděná data telemetrie zahrnují následující:

* Výjimky, ke kterým dochází během provádění Azure Log Integration.
* Metriky o počtu zpracovaných dotazů a událostech.
* Statistika, které slouží k použití možností příkazového řádku Azlog. exe. 

> [!NOTE]
> Doporučujeme, abyste Microsoftu povolili shromažďování dat telemetrie. Shromažďování dat telemetrie můžete vypnout tak, že zrušíte zaškrtnutí políčka **Povolit Microsoftu shromažďovat data telemetrie** .
>

![Snímek obrazovky s oknem instalace se zaškrtnutým políčkem telemetrie](./media/azure-log-integration-get-started/telemetry.png)


Proces instalace je popsaný v tomto videu:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Kroky po instalaci a ověření

Po dokončení základní instalace jste připraveni provést kroky po instalaci a ověření:

1. Spusťte PowerShell jako správce. Pak otevřete umístění C:\Program Files\Microsoft Azure Log Integration.
2. Importujte rutiny Azure Log Integration. Chcete-li importovat rutiny, spusťte skript `LoadAzlogModule.ps1`. Zadejte `.\LoadAzlogModule.ps1`a stiskněte klávesu ENTER (Všimněte si použití **.\\**  v tomto příkazu). Mělo by se zobrazit něco, co vypadá na následujícím obrázku:

   ![Snímek obrazovky výstupu příkazu LoadAzlogModule. ps1](./media/azure-log-integration-get-started/loaded-modules.png)
3. Dále nakonfigurujte Azure Log Integration pro použití konkrétního prostředí Azure. *Prostředí Azure* je typ cloudového datového centra Azure, se kterým chcete pracovat. I když existuje několik prostředí Azure, v současné době jsou relevantní možnosti buď **AzureCloud** , nebo **AzureUSGovernment**. Spusťte PowerShell jako správce a ujistěte se, že jste v adresáři C:\Program Files\Microsoft Azure log Integration\. Pak spusťte tento příkaz:

   `Set-AzlogAzureEnvironment -Name AzureCloud` (for **AzureCloud**)
  
   Pokud chcete použít cloudovou službu Azure pro státní správu USA, použijte **AzureUSGovernment** pro proměnnou **-Name** . V současné době nejsou podporovány jiné cloudy Azure.  

   > [!NOTE]
   > Po úspěšném provedení příkazu se vám nebude zobrazovat žádná zpětná vazba. 

4. Než budete moct monitorovat systém, budete potřebovat název účtu úložiště, který se používá pro Azure Diagnostics. V Azure Portal přejít na **virtuální počítače**. Vyhledejte virtuální počítač s Windows, který budete monitorovat. V části **vlastnosti** vyberte **nastavení diagnostiky**.  Pak vyberte **Agent**. Poznamenejte si název účtu úložiště, který je zadaný. Tento název účtu budete potřebovat pro pozdější krok.

   ![Snímek obrazovky s podoknem nastavení Azure Diagnostics](./media/azure-log-integration-get-started/storage-account-large.png) 

   ![Snímek obrazovky s tlačítkem povolit monitorování na úrovni hosta](./media/azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Pokud monitorování nebylo povoleno při vytvoření virtuálního počítače, můžete ho povolit, jak je znázorněno na předchozím obrázku.

5. Nyní se vraťte na Azure Log Integration počítač. Ověřte, že máte připojení k účtu úložiště ze systému, na který jste nainstalovali Azure Log Integration. Počítač, na kterém běží služba Azure Log Integration, potřebuje přístup k účtu úložiště, aby se načetly informace, které se protokolují Azure Diagnostics na každém monitorovaném systému. Ověření připojení: 
   1. [Stáhněte si Průzkumník služby Azure Storage](https://storageexplorer.com/).
   2. Dokončete nastavení.
   3. Po dokončení instalace vyberte **Další**. Nechejte zaškrtnuté políčko **spustit Průzkumník služby Microsoft Azure Storage** .  
   4. Přihlaste se k Azure.
   5. Ověřte, že se vám zobrazí účet úložiště, který jste nakonfigurovali pro Azure Diagnostics: 

   ![Snímek obrazovky s účty úložiště v Průzkumník služby Storage](./media/azure-log-integration-get-started/storage-explorer.png)

   1. V části účty úložiště se zobrazí několik možností. V části **tabulky**by se měla zobrazit tabulka s názvem **WADWindowsEventLogsTable**.

   Pokud monitorování nebylo povoleno při vytvoření virtuálního počítače, můžete ho povolit, jak je popsáno výše.


## <a name="integrate-windows-vm-logs"></a>Integrace protokolů virtuálních počítačů s Windows

V tomto kroku nakonfigurujete počítač, na kterém běží služba Azure Log Integration, aby se připojil k účtu úložiště, který obsahuje soubory protokolu.

K dokončení tohoto kroku potřebujete pár věcí:  
* **FriendlyNameForSource**: Popisný název, který můžete použít pro účet úložiště, který jste nakonfigurovali pro virtuální počítač k ukládání informací z Azure Diagnostics.
* **StorageAccountName**: Název účtu úložiště, který jste zadali při konfiguraci Azure Diagnostics.  
* **StorageKey**: Klíč úložiště pro účet úložiště, ve kterém jsou uložené informace o Azure Diagnostics pro tento virtuální počítač.  

Pokud chcete získat klíč úložiště, proveďte následující kroky:
1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V navigačním podokně vyberte **všechny služby**.
3. Do pole **Filtr** zadejte **Storage**. Pak vyberte **účty úložiště**.

   ![Snímek obrazovky, který zobrazuje účty úložiště ve všech službách](./media/azure-log-integration-get-started/filter.png)

4. Zobrazí se seznam účtů úložiště. Dvakrát klikněte na účet, který jste přiřadili k úložišti protokolu.

   ![Snímek obrazovky, který zobrazuje seznam účtů úložiště](./media/azure-log-integration-get-started/storage-accounts.png)

5. V části **Nastavení** vyberte **Přístupové klíče**.

   ![Snímek obrazovky, který zobrazuje možnost přístupové klíče v nabídce](./media/azure-log-integration-get-started/storage-account-access-keys.png)

6. Zkopírujte **klíč1**a pak ho uložte na bezpečném místě, ke kterému máte přístup v následujícím kroku.
7. Na serveru, na který jste nainstalovali Azure Log Integration otevřete okno příkazového řádku jako správce. (Nezapomeňte otevřít okno příkazového řádku jako správce, nikoli PowerShell).
8. Přejít do složky C:\Program Files\Microsoft Azure Log Integration.
9. Spusťte tento příkaz: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
   Příklad:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Pokud chcete, aby se ID předplatného zobrazilo v XML události, přidejte ID předplatného do popisného názvu:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Příklad:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Počkejte až 60 minut a pak zobrazte události, které se z účtu úložiště vyžádaly. Pokud chcete zobrazit události, v Azure log Integration vyberte **Prohlížeč událostí** > předané**události** **protokolu** > Windows.

Následující video se zabývá předchozími kroky:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Pokud se ve složce předané události nezobrazují data
Pokud se data ve složce předané události nezobrazují po hodinu, proveďte tyto kroky:

1. Podívejte se na počítač, na kterém je spuštěna služba Azure Log Integration. Potvrďte, že má přístup k Azure. Pokud chcete otestovat připojení, v prohlížeči se pokuste přejít na [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že uživatelský účet Azlog má oprávnění k zápisu pro složku users\Azlog.
   1. Otevřete Průzkumníka souborů.
   2. Přejít na C:\Users.
   3. Klikněte pravým tlačítkem na C:\users\Azlog.
   4. Vyberte **zabezpečení**.
   5. Vyberte **NT Service\Azlog**. Ověřte oprávnění pro tento účet. Pokud účet na této kartě chybí nebo pokud se příslušná oprávnění nezobrazují, můžete na této kartě udělit oprávnění k účtu.
3. Po spuštění příkazu `Azlog source list`se ujistěte, že je účet úložiště, který byl přidán do příkazu `Azlog source add` , uveden ve výstupu.
4. Pokud chcete zjistit, jestli jsou nějaké chyby hlášené od služby Azure log Integration, přejděte na **Prohlížeč událostí** > **Windows logs** > **aplikace**.

Pokud narazíte na problémy při instalaci a konfiguraci, můžete vytvořit [žádost o podporu](../../azure-supportability/how-to-create-azure-support-request.md). Pro tuto službu vyberte **log Integration**.

Další možností podpory je [Azure log Integration Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Ve fóru MSDN může komunita poskytovat podporu tím, že odpoví na dotazy a tipy a triky pro sdílení, jak získat z Azure Log Integration maximum. Tým Azure Log Integration také sleduje toto fórum. Pomůžou pokaždé, když můžou.

## <a name="integrate-azure-activity-logs"></a>Integrace protokolů aktivit Azure

Protokol aktivit Azure je předplatné protokol, který poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo v Azure. To zahrnuje rozsah dat, od Azure Resource Manager provozních dat až po aktualizace Service Healthch událostí. V tomto protokolu jsou také zahrnuty výstrahy Azure Security Center.
> [!NOTE]
> Než se pokusíte o kroky v tomto článku, musíte si projít [článek Začínáme](azure-log-integration-get-started.md) a postupovat tam.

### <a name="steps-to-integrate-azure-activity-logs"></a>Postup integrace protokolů aktivit Azure

1. Otevřete příkazový řádek a spusťte tento příkaz:```cd c:\Program Files\Microsoft Azure Log Integration```
2. Spusťte tento příkaz:```azlog createazureid```

    Tento příkaz vás vyzve k přihlášení do Azure. Příkaz potom vytvoří Azure Active Directory instančního objektu v klientech Azure AD, kteří jsou hostiteli předplatných Azure, ve kterých je přihlášený uživatel správcem, spolusprávcem nebo vlastníkem. Pokud je přihlášený uživatel jenom uživatel typu Host v tenantovi Azure AD, příkaz se nezdaří. Ověřování do Azure se provádí prostřednictvím služby Azure AD. Vytvoření instančního objektu pro Azure Log Integration vytvoří identitu služby Azure AD, která má přístup ke čtení z předplatných Azure.
3. Spusťte následující příkaz pro autorizaci instančního objektu Azure Log Integration, který jste vytvořili v předchozím kroku, a přihlaste se k části Přečtěte si protokol aktivit daného předplatného. Aby bylo možné příkaz Spustit, musíte být vlastníkem předplatného.

   ```Azlog.exe authorize subscriptionId```Případě

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. Zkontrolujte následující složky a potvrďte, že se v nich vytvoří soubory JSON protokolu auditu Azure Active Directory:
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Konkrétní pokyny k přenosu informací do souborů JSON do systému správy událostí a informací o zabezpečení (SIEM) získáte od dodavatele SIEM.

Pomoc komunity je k dispozici prostřednictvím [Azure log Integration fóra MSDN](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Tato Fórum umožňuje lidem v Azure Log Integration komunitě podporovat otázky, odpovědi, tipy a triky. Kromě toho tým Azure Log Integration monitoruje toto fórum a pomůže vám to pokaždé, když to může.

Můžete také otevřít [žádost o podporu](../../azure-supportability/how-to-create-azure-support-request.md). Jako službu, pro kterou požadujete podporu, vyberte integrace protokolu.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Log Integration najdete v následujících článcích: Než se pokusíte o kroky v tomto článku, musíte si projít článek Začínáme a postupovat tam.

* [Seznámení s Azure log Integration](azure-log-integration-overview.md). Tento článek vás seznámí s Azure Log Integration, jeho klíčovými schopnostmi a jak funguje.
* [Kroky konfigurace partnerského serveru](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Tento Blogový příspěvek ukazuje, jak nakonfigurovat Azure Log Integration pro práci s partnerskými řešeními Splunk, HP ArcSight a IBM QRadar. Popisuje naše aktuální pokyny ke konfiguraci komponent SIEM. Další podrobnosti vám poskytne dodavatel SIEM.
* Nejčastější [dotazy Azure log Integration](azure-log-integration-faq.md) Tato Nejčastější dotazy vám poodpoví na běžné otázky týkající se Azure Log Integration.
* [Integrace výstrah Azure Security Center s Azure log Integration](/azure/security-center/security-center-integrating-alerts-with-log-integration). V tomto článku se dozvíte, jak synchronizovat výstrahy Security Center a události zabezpečení virtuálních počítačů, které jsou shromažďovány pomocí Azure Diagnostics a protokolů aktivit Azure. Protokoly synchronizujete pomocí Azure Monitorch protokolů nebo řešení SIEM.
* [Nové funkce pro Azure Diagnostics a protokoly auditu Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) Tento Blogový příspěvek vás seznámí s protokoly auditu Azure a dalšími funkcemi, které vám pomůžou získat přehled o operacích vašich prostředků Azure.
