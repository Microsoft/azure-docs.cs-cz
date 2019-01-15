---
title: Začínáme s integrací protokolů Azure | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat službu Azure Log Integration a integrace protokolů ze služby Azure Storage, protokoly auditování Azure a výstrahy Azure Security Center.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: bf39e22c0edc23d2e452d96103c8933e0a47af9e
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304405"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integrace protokolů Azure s protokolování diagnostiky Azure a předávání událostí Windows


>[!IMPORTANT]
> Funkce integrace protokolů Azure se přestanou používat podle 06/01/2019. Soubory ke stažení AzLog byly deaktivovány 27. června 2018. Pokyny, jak postupovat přesun vpřed revizi, příspěvek [použití Azure monitoru k integraci s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integrace protokolů Azure měli používat jenom v případě [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) konektor není k dispozici od dodavatele incidentů zabezpečení a správu událostí (SIEM).

Integrace protokolů Azure zpřístupňuje protokoly Azure do vašeho systému SIEM, můžete vytvořit řídicí panel jednotné zabezpečení pro všechny vaše prostředky.
Další informace o stavu konektoru služby Azure Monitor obraťte se na dodavatele vašeho systému SIEM.

> [!IMPORTANT]
> Pokud primárním zájmem je shromažďování protokolů virtuálního počítače, většina dodavatelů SIEM zahrnout tuto možnost jejich řešení. Použití systému SIEM od dodavatele konektoru je vždy upřednostňovaná alternativa.

Tento článek vám pomůže začít pracovat s integrací protokolů Azure. Zaměřuje se na instalaci služby Azure Log Integration a integraci služby s využitím Azure Diagnostics. Služba pro integraci protokolů Azure potom shromažďuje informace z protokolu událostí Windows z kanálu Windows události zabezpečení z virtuálních počítačů nasazených v Azure infrastruktury jako služby. To se podobá *předávání událostí* , která může používat v místním systému.

> [!NOTE]
> Výstup Azure Log Integration integrování SIEM se provádí samotného systému SIEM. Další informace najdete v tématu [integrovat integrace protokolů Azure s řešením SIEM v místním](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Služba Azure Log Integration běží na fyzický nebo virtuální počítač se systémem Windows Server 2008 R2 nebo novější (Windows Server 2016 nebo Windows Server 2012 R2 je upřednostňované).

Fyzický počítač lze spouštět místně nebo na hostitelském serveru. Pokud budete chtít spustit službu Azure Log Integration na virtuálním počítači, virtuální počítač může být místní nebo ve veřejném cloudu, jako je například Microsoft Azure.

Fyzický nebo virtuální počítač se službou Azure Log Integration vyžaduje připojení k síti na veřejném cloudu Azure. Tento článek obsahuje podrobné informace o požadované konfigurace.

## <a name="prerequisites"></a>Požadavky

Instalace integrace protokolů Azure vyžaduje minimálně následující položky:

* **Předplatného Azure**. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* A **účtu úložiště** , který lze použít pro Windows Azure Diagnostics (WAD) protokolování. Můžete použít účet předkonfigurovaného úložiště nebo vytvořit nový účet úložiště. Dále v tomto článku zjistíte, jak nakonfigurovat účet úložiště.

  > [!NOTE]
  > V závislosti na vašem scénáři nemusí být nutná účtu úložiště. Pro scénáře Azure Diagnostics popsaná v tomto článku účet úložiště je povinný.

* **Dva systémy**: 
  * Počítač, na kterém běží služba pro integraci protokolů Azure. Tento počítač shromáždí všechny informace protokolu, který později importovat do vašeho systému SIEM. Tento systém:
    * Může být místní nebo hostovaný v Microsoft Azure.  
    * Musí být spuštěn x x64 verzi systému Windows Server 2008 R2 SP1 nebo novější, a mít rozhraní Microsoft .NET 4.5.1 nainstalované. Pokud chcete zjistit nainstalovanou verzi rozhraní .NET, naleznete v tématu [určit, jaké verze rozhraní .NET Framework jsou nainstalovány](https://msdn.microsoft.com/library/hh925568).  
    * Musí mít připojení k účtu služby Azure Storage, která byla použita pro protokolování diagnostiky Azure. Dále v tomto článku zjistíte, jak potvrdíte připojení.
  * Počítač, který chcete monitorovat. Toto je spuštěná jako virtuální počítač [virtuálních počítačů Azure](../virtual-machines/virtual-machines-windows-overview.md). Protokolování informací z tohoto počítače je odeslána do počítače Služba integrace protokolů Azure.

Rychlé ukázku toho, jak vytvořit virtuální počítač pomocí webu Azure portal podívejte se na následující video:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Aspekty nasazování

Během testování, můžete použít libovolný systém, který splňuje požadavky na minimální verzi operačního systému. Pro produkční prostředí zatížení může vyžadovat naplánovat vertikální navýšení kapacity nebo horizontální navýšení kapacity.

Můžete spustit více instancí služby Azure Log Integration. Však můžete spustit pouze jedna instance služby na fyzický nebo virtuální počítač. Kromě toho můžete účty úložiště – nástroj pro vyrovnávání zatížení Azure Diagnostics pro WAD. Počet předplatných poskytovat instance, která vychází z vaší kapacity.

> [!NOTE]
> V současné době nemáme konkrétní doporučení o tom, kdy horizontálně navýšit kapacitu instancí počítačů integrace protokolů Azure (to znamená, počítače se službou Azure Log Integration) nebo pro účty úložiště nebo předplatných. Rozhodovat škálování na základě vaše připomínky výkon v každé z těchto oblastí.

Chcete-li zvýšit výkon, máte také možnost vertikálně navýšit kapacitu služby Azure Log Integration. Tyto metriky výkonu může pomoci nastavit velikost počítače, které můžete spustit služba pro integraci protokolů Azure:

* Na počítači 8 procesorů (jader) jedna instance Azure Log Integration může zpracovat přibližně 24 milionů událostí za den (přibližně 1 milion událostí za hodinu).
* Na počítači s procesorem 4 (core) jedna instance Azure Log Integration může zpracovat asi 1,5 milionu událostí za den (přibližně 62,500 událostí za hodinu).

## <a name="install-azure-log-integration"></a>Instalace integrace protokolů Azure

Spuštění prostřednictvím nastavení rutina. Zvolte, zda chcete poskytnout informace o telemetrii do Microsoftu.

Služba Azure Log Integration shromažďuje telemetrická data z počítače, na kterém je nainstalována.  

Telemetrická data, která se shromažďují zahrnuje následující položky:

* Výjimky, ke kterým dochází při provádění integrace protokolů Azure.
* Metriky o počtu dotazů a události byly zpracovány.
* Statistiky, o které Azlog.exe se používají možnosti příkazového řádku. 

> [!NOTE]
> Doporučujeme povolit Microsoftu shromažďovat telemetrická data. Můžete vypnout shromažďování telemetrických dat tím, že zrušíte **povolit Microsoftu shromažďování telemetrických dat** zaškrtávací políčko.
>

![Snímek obrazovky podokna instalace se zaškrtnutým políčkem telemetrie](./media/security-azure-log-integration-get-started/telemetry.png)


Postup instalace najdete v následujícím videu:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Kroky po instalaci a ověření

Po dokončení základní nastavení jste připraveni k provedení kroků po instalaci a ověření:

1. Spusťte PowerShell jako správce. Přejděte do C:\Program Files\Microsoft Azure Log Integration.
2. Importujte rutiny integrace protokolů Azure. Chcete-li importovat rutiny, spusťte skript `LoadAzlogModule.ps1`. Zadejte `.\LoadAzlogModule.ps1`, a potom stiskněte klávesu Enter (Všimněte si použití **.\\**  v tomto příkazu). By měl vypadat přibližně jako co se zobrazuje na následujícím obrázku:

  ![Snímek obrazovky výstupu příkazu LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. V dalším kroku nakonfigurujte integrace protokolů Azure použít konkrétní prostředí Azure. *Prostředí Azure* je typ datového centra v cloudu Azure, kterou chcete pracovat. I když existuje několik prostředí Azure, aktuálně, příslušné možnosti jsou buď **AzureCloud** nebo **AzureUSGovernment**. Spouštění prostředí PowerShell jako správce, ujistěte se, že jste v C:\Program Files\Microsoft Azure Log Integration\. Spusťte tento příkaz:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (for **AzureCloud**)
  
  Pokud chcete použít cloud Azure státní správy USA, použijte **AzureUSGovernment** pro **– název** proměnné. Další cloudy Azure v současné době nejsou podporovány.  

  > [!NOTE]
  > Po úspěšném provedení příkazu není přijímat zpětnou vazbu. 

4. Před systému můžete monitorovat, je třeba název účtu úložiště, který se používá Azure Diagnostics. Na webu Azure Portal, přejděte na **virtuálních počítačů**. Vyhledejte virtuální počítač Windows, který bude monitorovat. V **vlastnosti** vyberte **nastavení diagnostiky**.  Vyberte **agenta**. Poznamenejte si název účtu úložiště, který je zadán. Musíte tento název účtu pro později.

  ![Snímek obrazovky podokna nastavení diagnostiky Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Snímek obrazovky povolit monitorování tlačítko úrovni hosta](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Pokud při vytváření virtuálního počítače není zapnuté monitorování, můžete ji povolit, jak je znázorněno na předchozím obrázku.

5. Nyní přejděte zpět na počítač integrace protokolů Azure. Ověřte, že máte připojení k účtu úložiště v systému nainstalovaným integrace protokolů Azure. Počítači se službou Azure Log Integration potřebuje přístup k účtu úložiště k načtení informací, která je zaznamenána pomocí Azure Diagnostics na každém monitorovaném systému. Pokud chcete ověřit připojení: 
  1. [Stáhněte si Průzkumníka služby Azure Storage](http://storageexplorer.com/).
  2. Dokončete instalaci.
  3. Po dokončení instalace, vybrat **Další**. Nechte **spusťte Průzkumníka služby Microsoft Azure Storage** zaškrtnuté políčko.  
  4. Přihlaste se k Azure.
  5. Ověřte, že vidíte účet úložiště, který jste nakonfigurovali Azure Diagnostics: 

   ![Snímek obrazovky s účty úložiště v Průzkumníkovi služby Storage](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Tyto možnosti se zobrazí pod účty úložiště. V části **tabulky**, měli byste vidět tabulky nazvané **WADWindowsEventLogsTable**.

  Pokud při vytváření virtuálního počítače není zapnuté monitorování, umožníte, jak je popsáno výše.


## <a name="integrate-windows-vm-logs"></a>Integrace protokolů virtuálních počítačů Windows

V tomto kroku nakonfigurujete počítači spuštěna služba Azure Log Integration pro připojení k účtu úložiště, který obsahuje soubory protokolu.

K provedení tohoto kroku potřebujete několik věcí:  
* **FriendlyNameForSource**: Popisný název, můžete provést u účtu úložiště, že jste nakonfigurovali pro virtuální počítač k ukládání informací z Azure Diagnostics.
* **StorageAccountName**: Název účtu úložiště, které jste zadali při konfiguraci diagnostiky Azure.  
* **StorageKey**: Klíč úložiště pro účet úložiště, kde jsou uloženy informace diagnostiky Azure pro tento virtuální počítač.  

Pokud chcete získat klíč úložiště, proveďte následující kroky:
1. Přejděte na [Azure Portal](http://portal.azure.com).
2. V navigačním podokně vyberte **všechny služby**.
3. V **filtr** zadejte **úložiště**. Vyberte **účty úložiště**.

  ![Snímek obrazovky s účty úložiště ve všech služeb](./media/security-azure-log-integration-get-started/filter.png)

4. Zobrazí se seznam účtů úložiště. Dvakrát klikněte na účet, který jste přiřadili k protokolování úložiště.

  ![Snímek obrazovky zobrazující seznam účtů úložiště](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. V části **Nastavení** vyberte **Přístupové klíče**.

  ![Snímek obrazovky zobrazující možnost přístup klíče v nabídce](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Kopírování **key1**a pak ho uložte na bezpečné místo, které lze použít pro následující krok.
7. Na serveru, kam jste nainstalovali Azure Log Integration otevřete okno příkazového řádku jako správce. (Nezapomeňte otevřete okno příkazového řádku jako správce a ne na prostředí PowerShell).
8. Přejděte do C:\Program Files\Microsoft Azure Log Integration.
9. Spusťte tento příkaz: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Příklad:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Pokud chcete zobrazit události XML ID předplatného, připojte ID předplatného na popisný název:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Příklad:
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Počkejte, až 60 minut a pak zobrazit události, které se berou z účtu úložiště. Pokud chcete zobrazit události, v Azure Log Integration, vyberte **Prohlížeč událostí** > **protokoly Windows** > **předané události**.

V následujícím videu se probírají v předchozích krocích:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Pokud se nezobrazuje data ve složce předané události
Pokud data se nezobrazuje ve složce předané události po hodině, proveďte tyto kroky:

1. Zkontrolujte počítač, na kterém běží služba pro integraci protokolů Azure. Potvrďte, že může přístup k Azure. Chcete-li otestovat připojení, v prohlížeči, zkuste přejít na [webu Azure portal](https://portal.azure.com).
2. Ujistěte se, že uživatelský účet Azlog má oprávnění k zápisu pro složku users\Azlog.
  1. Otevřete Průzkumníka souborů.
  2. Přejdete na C:\users.
  3. Right-click C:\users\Azlog.
  4. Vyberte **zabezpečení**.
  5. Vyberte **NT Service\Azlog**. Zkontrolujte oprávnění pro účet. Pokud účet není na této kartě, nebo pokud se zobrazí příslušná oprávnění, můžete udělit oprávnění účtu na této kartě.
3. Při spuštění příkazu `Azlog source list`, ujistěte se, že účet úložiště, který byl přidán v příkazu `Azlog source add` je uvedená ve výstupu.
4. Pokud chcete zobrazit, pokud se ohlásí chyby ze služby Azure Log Integration, přejděte na **Prohlížeč událostí** > **protokoly Windows** > **aplikace**.

Pokud narazíte na jakékoli problémy během instalace a konfigurace, můžete vytvořit [žádost o podporu](../azure-supportability/how-to-create-azure-support-request.md). Pro služby, vyberte **integrace protokolů**.

Další možností podpory [fóru pro Azure Log Integration MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Ve fóru MSDN můžete v komunitě poskytovat podporu odpovídání na dotazy a tipy a triky o tom, jak naplno využít Azure Log Integration pro sdílení obsahu. Tým Azure Log Integration také sleduje tohoto fóra. Pomáhají pokaždé, když se to jde.

## <a name="integrate-azure-activity-logs"></a>Integrace protokolů aktivit Azure

Protokol aktivit Azure je předplatné protokol, který poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo v Azure. To zahrnuje celou řadu dat z Azure Resource Manageru provozních dat k aktualizacím na události služby Service Health. Výstrahy služby Azure Security Center jsou také zahrnuté v tomto protokolu.
> [!NOTE]
> Než se pokusíte kroky v tomto článku, musíte zkontrolovat [Začínáme](security-azure-log-integration-get-started.md) článek a proveďte nutné kroky existuje.

### <a name="steps-to-integrate-azure-activity-logs"></a>Postup integrace protokolů aktivit Azure

1. Otevřete příkazový řádek a spusťte tento příkaz:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Spuštěním tohoto příkazu:  ```azlog createazureid```

    Tento příkaz vás vyzve k zadání přihlášení Azure. Příkaz vytvoří službu Azure Active Directory instančního objektu v tenantů Azure AD, které jsou hostiteli předplatná Azure, ve kterých přihlášený uživatel je správce, spolusprávce nebo vlastníka. Příkaz se nezdaří, pokud je uživatel přihlášený jenom uživatele typu Host do tenanta Azure AD. Ověřování do Azure se provádí prostřednictvím Azure AD. Při vytváření instančního objektu pro Azure Log Integration vytvoří identity Azure AD, který je přiřazen přístup ke čtení z předplatných Azure.
3.  Spusťte následující příkaz k autorizaci instančního objektu služby Azure Log Integration vytvořili v předchozím kroku přístup k protokolu aktivit pro čtení pro předplatné. Musíte být vlastníkem předplatného pro spuštění příkazu.

    ```Azlog.exe authorize subscriptionId``` Příklad:

```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4.  Zkontrolujte následující složky potvrďte, že jsou v nich vytvořili soubory JSON protokolů auditu Azure Active Directory:
    - C:\Users\azlog\AzureResourceManagerJson
    - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Konkrétní pokyny k uvedení informace v souborech JSON do systému pro správu (SIEM) událostí a informací o zabezpečení obraťte se na dodavatele vašeho systému SIEM.

Je k dispozici prostřednictvím pomoc komunity [fórum na webu MSDN Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Toto fórum umožňuje lidem v komunitě Azure Log Integration pro podporu vzájemně otázky, odpovědi, tipy a triky. Kromě toho tým Azure Log Integration Toto fórum monitoruje a pomáhá kdykoli je to možné.

Můžete také otevřít [žádost o podporu](../azure-supportability/how-to-create-azure-support-request.md). Integrace protokolů vyberte jako službu, pro kterou jsou žádosti o podporu.

## <a name="next-steps"></a>Další postup

Další informace o Azure Log Integration, naleznete v následujících článcích: Nežli se pokusíte kroky v tomto článku, musíte k tomuto článku Get Začínáme a dokončete kroky existuje.

* [Úvod do integrace protokolů Azure](security-azure-log-integration-overview.md). Tento článek vás seznámí s integrací protokolů Azure, jejích klíčových funkcích a jak to funguje.
* [Partner kroky konfigurace](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Tento příspěvek na blogu ukazuje postup při konfiguraci integrace protokolů Azure pro práci s partnerskými řešeními Splunk, HP ArcSight a IBM QRadar. Popisuje naše aktuální doprovodné materiály, o tom, jak nakonfigurovat komponenty systému SIEM. Zkontrolovat u dodavatele systému SIEM k další podrobnosti.
* [Integrace protokolů Azure – nejčastější dotazy (FAQ)](security-azure-log-integration-faq.md). Tyto nejčastější dotazy odpovědi na běžné dotazy týkající se integrace protokolů Azure.
* [Integrace výstrah služby Azure Security Center s integrací protokolů Azure](../security-center/security-center-integrating-alerts-with-log-integration.md). V tomto článku se dozvíte, jak synchronizovat výstrahy Security Center a události zabezpečení virtuálního počítače, které byly shromážděny sadou diagnostiky Azure a aktivit v Azure protokoly. Protokoly se synchronizuje s využitím řešení Azure Log Analytics nebo systému SIEM.
* [Protokoly auditu nových funkcí pro diagnostiku Azure a Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Tento příspěvek na blogu vás seznámí s protokoly auditování Azure a dalších funkcí, které vám může pomoct získat přehled o operacích vašich prostředků Azure.
