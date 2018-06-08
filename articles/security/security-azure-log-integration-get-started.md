---
title: Začínáme s Azure protokolu integrace | Microsoft Docs
description: Zjistěte, jak nainstalovat službu protokolu integrace se službou Azure a integrovat protokoly z Azure Storage, Azure auditování, výstrahy a protokolování Azure Security Center.
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
ms.date: 06/06/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: b8888823b1445dc084ae4c0323d90110c9d384a4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839441"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Protokol integrace se službou Azure s Azure Diagnostics protokolování a předávání událostí systému Windows

>[!IMPORTANT]
> Funkce integrace protokolu Azure bude 06/01/2019 zastaralá.  AzLog stahování bude ve 27. června 2018 zakázáno. Pokyny k tomu, co udělat přesunutí dopředného zkontrolujte v příspěvku [monitorování pomocí Azure, který chcete integrovat s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/preview/?cdn=disable) 

Integrace Azure protokolu měli používat jenom v případě [Azure monitorování](../monitoring-and-diagnostics/monitoring-get-started.md) konektor není k dispozici od dodavatele incidentu zabezpečení a událostí správy (SIEM).

Integrace se službou Azure protokolu zpřístupní Azure protokoly do vašeho systému SIEM, můžete vytvořit řídicí panel jednotná zabezpečení pro všechny prostředky.
Další informace o stavu konektoru služby Azure monitorování obraťte se na dodavatele systému SIEM.

> [!IMPORTANT]
> Pokud vaše primární týkající se shromažďování protokolů virtuálního počítače, většina dodavatelů SIEM zahrnout tuto možnost jejich řešení. Použití systému SIEM dodavatele konektor je vždy upřednostňované alternativní.

Tento článek vám pomůže začít pracovat s protokolu integrace se službou Azure. Zaměřuje se na instalaci služby protokolu integrace se službou Azure a integrování služba Azure Diagnostics. Služba integrace se službou Azure protokolu pak shromažďuje informace protokolu událostí systému Windows z tohoto kanálu událostí zabezpečení systému Windows z virtuálních počítačů nasazených v Azure infrastruktura jako služba. Toto je podobná *předávání událostí* , můžete použít v místním systému.

> [!NOTE]
> Integrování výstup protokolu integrace se službou Azure SIEM je potřeba systému SIEM sám sebe. Další informace najdete v tématu [integrovat Azure protokolu integrace s vašeho systému SIEM místní](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Integrace se službou Azure protokolu služba bude spuštěna na fyzický nebo virtuální počítač se systémem Windows Server 2008 R2 nebo novější (Windows Server 2016 nebo Windows Server 2012 R2 je upřednostňovaný).

Fyzický počítač můžete spustit místně nebo na hostitelském serveru. Pokud zvolíte protokol integrace se službou Azure službu spustit na virtuálním počítači, virtuální počítač může být nacházejí na místních nebo ve veřejném cloudu, například Microsoft Azure.

Fyzický nebo virtuální počítač, spuštěna služba integrace se službou protokolu Azure vyžaduje připojení k síti na veřejném cloudu Azure. Tento článek obsahuje podrobné informace o požadované konfigurace.

## <a name="prerequisites"></a>Požadavky

Instalace protokolu integrace se službou Azure vyžaduje minimálně následující položky:

* **Předplatné**. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* A **účet úložiště** který lze použít pro Windows Azure Diagnostics (WAD) protokolování. Můžete použít účet předkonfigurované úložiště nebo vytvořit nový účet úložiště. Později v tomto článku jsme popisují, jak nakonfigurovat účet úložiště.

  > [!NOTE]
  > V závislosti na scénáři nemusí být vyžaduje účet úložiště. U Azure Diagnostics scénáři popsaná v tomto článku se vyžaduje účet úložiště.

* **Dvěma systémy**: 
  * Počítač, který spouští službu protokolu integrace se službou Azure. Tento počítač shromažďuje všechny informace protokolu, který později importovat do vašeho systému SIEM. Tento systém:
    * Může být místní nebo hostovaný v Microsoft Azure.  
    * Musí být spuštěna x64 verzi systému Windows Server 2008 R2 SP1 nebo novější, a mít rozhraní Microsoft .NET 4.5.1 nainstalována. Nainstalovaná verze rozhraní .NET, zjistíte v [zjištění nainstalovaných verzí rozhraní .NET Framework](https://msdn.microsoft.com/library/hh925568).  
    * Musí mít připojení k účtu úložiště Azure, který byl použit pro protokolování diagnostiky Azure. Později v tomto článku jsme popisují, jak potvrdíte připojení.
  * Počítač, který chcete monitorovat. Toto je virtuální počítač spuštěn jako [virtuální počítač Azure](../virtual-machines/virtual-machines-windows-overview.md). Protokolování informací z tohoto počítače je odeslána do počítače Služba integrace se službou Azure protokolu.

Pro rychlé předvedení toho, jak vytvořit virtuální počítač pomocí portálu Azure prohlédněte si následující video:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Aspekty nasazování

Během testování, můžete použít libovolný systém, který splňuje požadavky na minimální verzi operačního systému. Pro produkční prostředí zatížení může vyžadovat naplánovat vertikálním navýšení kapacity nebo škálování.

Můžete spustit víc instancí služby protokolu integrace se službou Azure. Můžete však spustit pouze jednu instanci služby na fyzický nebo virtuální počítač. Kromě toho můžete pro WAD účty úložiště Azure Diagnostics Vyrovnávání zatížení. Počet odběrů k poskytování instance vychází z vaší kapacity.

> [!NOTE]
> V současné době nemáme konkrétní doporučení o tom, kdy chcete škálovat instance počítačů protokolu integrace se službou Azure (to znamená, počítače se službou protokolu integrace se službou Azure) nebo pro účty úložiště nebo předplatných. Rozhodnutí škálování podle vaše připomínky výkonu v každé z těchto oblastí.

Pro zlepšení výkonu, máte také možnost škálování služby protokolu integrace se službou Azure. Následující metriky výkonu vám může pomoct velikost počítače, které chcete spustit služba integrace se službou Azure protokolu:

* Na počítači 8 procesor (základní) jednu instanci protokolu integrace se službou Azure může zpracovat přibližně 24 milión událostí za den (přibližně 1 milion událostí za hodinu).
* Na počítači 4procesoru (základní) jednu instanci protokolu integrace se službou Azure může zpracovat asi 1,5 milionu událostí za den (přibližně 62,500 událostí za hodinu).

## <a name="install-azure-log-integration"></a>Nainstalujte integrační protokolů Azure

Chcete-li nainstalovat Azure protokolu integrace, stáhnout [integrace se službou Azure protokolu](https://www.microsoft.com/download/details.aspx?id=53324) instalační soubor. Dokončení procesu instalace. Vyberte, zda chcete zadat telemetrické informace společnosti Microsoft.

Služba integrace protokolu Azure shromažďuje telemetrická data z počítače, na kterém je nainstalována.  

Telemetrická data, která se shromažďují zahrnuje následující položky:

* Výjimky, ke kterým došlo během provádění protokolu integrace se službou Azure.
* Metriky o počtu dotazy a zpracování události.
* Statistiky, o které Azlog.exe se používají možnosti příkazového řádku. 

> [!NOTE]
> Doporučujeme vám, že povolíte společnosti Microsoft shromažďovat telemetrická data. Shromažďování telemetrických dat můžete vypnout tak, že smažete **Povolit společnosti Microsoft shromažďovat telemetrická data** zaškrtávací políčko.
>

![Snímek obrazovky podokna instalace se zaškrtnutým políčkem telemetrie](./media/security-azure-log-integration-get-started/telemetry.png)


Proces instalace je popsaná v následujícím videu:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Kroky po instalaci a ověření

Po dokončení základní nastavení, jste připraveni k provedení kroků po instalaci a ověření:

1. Spusťte PowerShell jako správce. Potom přejděte na C:\Program Files\Microsoft protokolu integrace se službou Azure.
2. Importujte rutiny protokolu integrace se službou Azure. Chcete-li importovat rutiny, spusťte skript `LoadAzlogModule.ps1`. Zadejte `.\LoadAzlogModule.ps1`, a stiskněte klávesu Enter (Všimněte si použití **.\\**  v tomto příkazu). Měli byste vidět něco podobného jako co se zobrazí na následujícím obrázku:

  ![Snímek obrazovky výstup příkazu LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. V dalším kroku nakonfigurujte integrace se službou protokolu Azure použít konkrétní prostředí Azure. *Prostředí Azure* je typ cloudu Azure datacenter, kterou chcete pracovat. I když existují aktuálně několik prostředí Azure, příslušné možnosti jsou buď **AzureCloud** nebo **AzureUSGovernment**. Spustit PowerShell jako správce, ujistěte se, že jste v C:\Program Files\Microsoft Azure protokolu Integration\. Spusťte tento příkaz:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (pro **AzureCloud**)
  
  Pokud chcete použít cloudové US Government Azure, použijte **AzureUSGovernment** pro **-název** proměnné. Aktuálně nejsou podporované ostatních cloudů Azure.  

  > [!NOTE]
  > Při úspěšném provedení příkazu není zobrazí zpětnou vazbu. 

4. Aby bylo možné sledovat systému, je třeba název účtu úložiště, který se používá pro Azure Diagnostics. V portálu Azure přejděte do **virtuální počítače**. Vyhledejte virtuálního počítače s Windows, který budete sledovat. V **vlastnosti** vyberte **nastavení pro diagnostiku**.  Pak vyberte **agenta**. Poznamenejte si název účtu úložiště, který je zadán. Je třeba tento název účtu pro později.

  ![Snímek obrazovky podokna nastavení diagnostiky Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Snímek obrazovky povolit monitorování tlačítko úrovni hosta](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Pokud při vytvoření virtuálního počítače není zapnuté monitorování, můžete ji povolit, jak je vidět na předchozím obrázku.

5. Nyní přejděte zpět na počítač protokolu integrace se službou Azure. Ověřte, že máte připojení k účtu úložiště ze systému nainstalovanou protokolu integrace se službou Azure. Počítači je spuštěna služba integrace se službou protokolu Azure potřebuje přístup k účtu úložiště k načtení informací, který je přihlášen pomocí Azure Diagnostics jednotlivé monitorované systémy. K ověření připojení: 
  1. [Stažení Azure Storage Explorer](http://storageexplorer.com/).
  2. Dokončete instalaci.
  3. Po dokončení instalace, vyberte **Další**. Ponechte **spusťte Microsoft Azure Storage Explorer** zaškrtnuté políčko.  
  4. Přihlaste se k Azure.
  5. Ověřte, zda se zobrazí účet úložiště, který jste nakonfigurovali pro Azure Diagnostics: 

   ![Snímek obrazovky účty úložiště ve Storage Exploreru](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Tyto možnosti se zobrazí pod účty úložiště. V části **tabulky**, měli byste vidět tabulce s názvem **WADWindowsEventLogsTable**.

  Pokud při vytvoření virtuálního počítače není zapnuté monitorování, můžete ji můžete povolit, jak bylo popsáno výše.


## <a name="integrate-windows-vm-logs"></a>Integrovat protokoly systému Windows virtuálního počítače

V tomto kroku nakonfigurujete počítači spuštěna služba integrace se službou Azure protokol pro připojení k účtu úložiště, který obsahuje soubory protokolu.

K dokončení tohoto kroku, je třeba pár věcí:  
* **FriendlyNameForSource**: popisný název, můžete použít k účtu úložiště, že jste nakonfigurovali pro virtuální počítač k ukládání informací z Azure Diagnostics.
* **StorageAccountName**: název účtu úložiště, kterou jste zadali při konfiguraci Azure Diagnostics.  
* **Klíč úložiště**: klíč úložiště pro účet úložiště se uloží informace o Azure Diagnostics pro tento virtuální počítač.  

Získat klíč úložiště, proveďte následující kroky:
1. Přejděte na [portál Azure](http://portal.azure.com).
2. V navigačním podokně vyberte **všechny služby**.
3. V **filtru** zadejte **úložiště**. Pak vyberte **účty úložiště**.

  ![Snímek obrazovky, který zobrazuje účty úložiště ve všech služeb](./media/security-azure-log-integration-get-started/filter.png)

4. Zobrazí se seznam účtů úložiště. Dvakrát klikněte na účet, který jste přiřadili k protokolování úložiště.

  ![Snímek obrazovky zobrazující seznam účtů úložiště](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. V části **Nastavení** vyberte **Přístupové klíče**.

  ![Snímek obrazovky, který ukazuje možnost klíče přístup v nabídce](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Kopírování **key1**a pak ho uložte na bezpečném místě, který je k dispozici pro následující krok.
7. Na serveru, kam jste nainstalovali protokolu integrace se službou Azure otevřete okno příkazového řádku jako správce. (Nezapomeňte otevřete okno příkazového řádku jako správce a ne prostředí PowerShell).
8. Přejděte do C:\Program Files\Microsoft Azure protokolu integrace.
9. Spusťte tento příkaz: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Příklad:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Pokud chcete ID předplatného objeví události XML, připojení k popisný název ID předplatného:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Příklad:
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Počkejte až 60 minut a pak zobrazit události, které jsou vyžádány z účtu úložiště. Chcete-li zobrazit události, v protokolu integrace se službou Azure, vyberte **Prohlížeč událostí** > **protokoly systému Windows** > **předávaných událostí**.

Následující video obsahuje předchozích kroků:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Pokud není zobrazovat data ve složce předávaných událostí
Pokud data ve složce předávaných událostí není zobrazovat po hodině, proveďte tyto kroky:

1. Zkontrolujte na počítač, který je spuštěna služba integrace se službou Azure protokolu. Potvrďte, že má přístup k Azure. Chcete-li otestovat připojení, v prohlížeči, zkuste přejít na [portál Azure](http://portal.azure.com).
2. Ujistěte se, že uživatelský účet Azlog má oprávnění k zápisu pro složku users\Azlog.
  1. Otevřete Průzkumníka souborů.
  2. Přejděte na C:\users.
  3. Klikněte pravým tlačítkem na C:\users\Azlog.
  4. Vyberte **zabezpečení**.
  5. Vyberte **NT Service\Azlog**. Zkontrolujte oprávnění pro účet. Pokud účet chybí na této kartě, nebo pokud se nezobrazují se všechna příslušná oprávnění, můžete udělit oprávnění účtu na této kartě.
3. Když spustíte příkaz `Azlog source list`, ujistěte se, že úložiště účtu, který byl přidán v příkazu `Azlog source add` je uvedena ve výstupu.
4. Pokud chcete zobrazit, pokud jsou hlášeny nějaké chyby ze služby protokolu integrace se službou Azure, přejděte na **Prohlížeč událostí** > **protokoly systému Windows** > **aplikace**.

Pokud narazíte na potíže během instalace a konfigurace, můžete vytvořit [žádost o podporu](../azure-supportability/how-to-create-azure-support-request.md). Pro službu, vyberte **integrace protokolu**.

Další možností podpory je [fórum MSDN integrace protokolu Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Ve fóru MSDN komunity zajistí podporu odpovědi na otázky a sdílení tipy a triky o tom, jak co nejlépe využít protokolu integrace se službou Azure. Integrace se službou Azure protokolu team také monitoruje tomto fóru. Mohou pomoci při každém mohou.

## <a name="integrate-azure-activity-logs"></a>Integrovat protokoly Azure aktivity

Protokol činnosti Azure je protokol odběru, který poskytuje přehled o události na úrovni předplatného, k nimž došlo v Azure. To zahrnuje celou řadu dat z provozních dat Azure Resource Manager aktualizací na události stavu služby. Azure Security Center výstrahy jsou také uvedené v tomto protokolu.
> [!NOTE]
> Před provedením kroků v tomto článku, je nutné si [Začínáme](security-azure-log-integration-get-started.md) článek a dokončete existuje.

### <a name="steps-to-integrate-azure-activity-logs"></a>Kroky pro integraci protokoly aktivita služby Azure

1. Otevřete příkazový řádek a spusťte tento příkaz:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Spusťte tento příkaz:  ```azlog createazureid```

    Tento příkaz zobrazí výzvu k přihlášení Azure. Příkaz vytvoří Azure Active Directory instančního objektu v klienty Azure AD, které jsou hostiteli předplatná Azure, ve kterých přihlášený uživatel je správce, spolusprávce nebo vlastníka. Příkaz se nezdaří, pokud je uživatel přihlášený pouze uživatel guest v klientovi Azure AD. Ověřování do Azure se provádí prostřednictvím služby Azure AD. Vytvoření objektu služby pro integraci protokolu Azure vytvoří Azure AD identity, který je přiřazen přístup ke čtení z předplatných Azure.
3.  Spusťte následující příkaz k autorizaci objekt služby protokolu integrace se službou Azure vytvořili v předchozí krok přístup ke čtení protokol aktivit pro předplatné. Musíte být vlastníkem u předplatného ke spuštění příkazu.

    ```Azlog.exe authorize subscriptionId``` Příklad:

```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```
4.  Zkontrolujte následující složky pro potvrzení, že jsou v nich vytvořeny soubory JSON protokolů auditu Azure Active Directory:
    - C:\Users\azlog\AzureResourceManagerJson
    - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Konkrétní pokyny k uvedení informací v souborech JSON do své informace o zabezpečení a událostí systému pro správu (SIEM) obraťte se na dodavatele systému SIEM.

Komunita pomoc je k dispozici prostřednictvím [fórum MSDN integrace protokolu Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Toto fórum umožňuje členové komunity protokolu integrace se službou Azure pro podporu navzájem otázky, odpovědi, tipy a triky. Kromě toho týmem protokolu integrace se službou Azure sleduje toto fórum a pomáhá vždy, když je to možné.

Můžete také otevřít [žádost o podporu](../azure-supportability/how-to-create-azure-support-request.md). Vyberte protokol integrace jako službu, pro kterou jsou žádosti o podporu.

## <a name="next-steps"></a>Další postup

Další informace o protokolu integrace se službou Azure, najdete v následujících článcích: před provedením kroků v tomto článku, musí si přečtěte článek Začínáme Get a dokončete existuje.

* [Azure protokolu integrace pro Azure protokoly](https://www.microsoft.com/download/details.aspx?id=53324). Stažení softwaru zahrnuje podrobnosti, požadavky na systém a pokyny k integraci Azure protokolu.
* [Úvod do integrace Azure protokolu](security-azure-log-integration-overview.md). Tento článek vás seznámí s protokolu integrace se službou Azure, jejích klíčových funkcích a jak to funguje.
* [Partner kroky konfigurace](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Tento příspěvek blogu ukazuje postup konfigurace integrace se službou protokolu Azure pro práci s partnerských řešení Splunk, HP ArcSight a IBM QRadar. Popisuje naše aktuální pokyny o tom, jak konfigurovat součásti systému SIEM. Poraďte s dodavatelem SIEM další podrobnosti.
* [Integrace se službou Azure protokolu nejčastější dotazy (FAQ)](security-azure-log-integration-faq.md). Tyto nejčastější dotazy odpovídá na běžné dotazy týkající se integrace se službou Azure protokolu.
* [Integrace Azure Security Center výstrahy s integrace se službou Azure protokolu](../security-center/security-center-integrating-alerts-with-log-integration.md). Tento článek ukazuje, jak synchronizovat výstrahy Security Center a události zabezpečení virtuálního počítače, které byly shromážděny sadou diagnostiky Azure a Azure aktivity protokoly. Protokoly synchronizujete pomocí řešení Azure Log Analytics nebo SIEM.
* [Protokoly auditu nové funkce pro diagnostiky Azure a Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Tento příspěvek blogu vás seznámí s protokoly auditu Azure a další funkce, které vám mohou pomoci získat přehled o činnosti vašich prostředků Azure.
