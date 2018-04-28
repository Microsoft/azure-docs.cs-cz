---
title: Konfigurace projektu Azure pomocí více konfigurace služby | Microsoft Docs
description: Zjistěte, jak nakonfigurovat změnou ServiceDefinition.csdef, ServiceConfiguration.Local.cscfg a ServiceConfiguration.Cloud.cscfg souborů projektu Azure cloud service.
services: visual-studio-online
author: ghogen
manager: douge
assetId: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 9047d7a8a6efdd41a48b6fa83b43a8c87d05d1de
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>Konfigurace projektu Azure v sadě Visual Studio používat více konfigurace služby

Projektu Azure cloud service v sadě Visual Studio obsahuje tři konfigurační soubory: `ServiceDefinition.csdef`, `ServiceConfiguration.Local.cscfg`, a `ServiceConfiguration.Cloud.cscfg`:

- `ServiceDefinition.csdef` nasazení do Azure k popisu požadavků Cloudová služba a její role a k poskytování nastavení, které se vztahují na všechny instance. Nastavení můžete přečíst v době běhu pomocí služby hostování modulu Runtime rozhraní API služby Azure. Tento soubor můžete aktualizovat v Azure, jenom v případě, že je Cloudová služba je zastavena.
- `ServiceConfiguration.Local.cscfg` a `ServiceConfiguration.Cloud.cscfg` zadejte hodnoty pro nastavení v definici souboru a zadejte počet instancí pro jednotlivé role. Soubor "Local" obsahuje hodnoty používané v místní ladění. soubor "Cloud" je nasadit do Azure jako `ServiceConfiguration.cscfg` a poskytuje nastavení pro prostředí serveru. Tento soubor můžete aktualizovat, když cloudové služby běží v Azure.

Nastavení konfigurace jsou spravovaná a upravovat v sadě Visual Studio pomocí stránky vlastností pro příslušné roli (klikněte pravým tlačítkem na roli a vyberte **vlastnosti**, nebo dvakrát kliknout na roli). Změny může být omezená na podle toho, která konfigurace je vybrán v **konfigurace služby** rozevíracího seznamu. Vlastnosti pro webové a pracovní role jsou podobné, s výjimkou kde popsané v následujících částech.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Informace o základní schémata pro definice služby a soubory konfigurace služby najdete v tématu [.csdef schématu XML](cloud-services/schema-csdef-file.md) a [.cscfg schématu XML](cloud-services/schema-cscfg-file.md) články. Další informace o konfiguraci služby najdete v tématu [postup konfigurace cloudové služby](cloud-services/cloud-services-how-to-configure-portal.md).


## <a name="configuration-page"></a>Stránka Konfigurace

### <a name="service-configuration"></a>Konfigurace služby

Vybere, které `ServiceConfiguration.*.cscfg` soubor je ovlivněný změny. Ve výchozím nastavení, jsou místní a cloudové variant a můžete **spravovat...**  příkaz pro kopírování, přejmenujte a odstraňte konfigurační soubory. Tyto soubory jsou přidány do projekt cloudové služby a zobrazují v **Průzkumníku řešení**. Ale přejmenování nebo odstranění konfigurace lze provést pouze z tohoto ovládacího prvku.

### <a name="instances"></a>Instance

Nastavte **Instance** počet vlastnosti tak, aby se počet instancí služby měly být spuštěny pro tuto roli.

Nastavte **velikost virtuálního počítače** vlastnost **navíc malé**, **malé**, **střední**, **velké**, nebo **Velmi velké**.  Další informace najdete v tématu [velikosti pro cloudové služby](cloud-services/cloud-services-sizes-specs.md).

### <a name="startup-action-web-role-only"></a>Po spuštění akce (jenom webové role)

Nastavte tuto vlastnost k určení, že by měl Visual Studio při spuštění ladění spustit webový prohlížeč, koncových bodů protokolu HTTP nebo HTTPS koncových bodů, nebo obojí.

**Koncový bod HTTPS** možnost je dostupná pouze v případě, že jste definovali koncový bod HTTPS již pro vaši roli. Můžete definovat koncový bod HTTPS na **koncové body** stránku vlastností.

Pokud jste už přidali koncový bod HTTPS, ve výchozím nastavení je povolena možnost koncový bod HTTPS a Visual Studio spustí prohlížeč pro tento koncový bod po spuštění ladění, kromě prohlížeče pro svůj koncový bod HTTP za předpokladu, že jsou povolené oba možnosti spuštění.

### <a name="diagnostics"></a>Diagnostika

Ve výchozím nastavení jsou diagnostiky povolené pro webovou roli. Účet služby Azure cloud projektu a úložiště jsou nastaveny na používat emulátor místního úložiště. Pokud budete chtít nasadit do Azure, můžete vybrat tlačítko Tvůrce (**...** ) místo toho použít úložiště Azure. Diagnostická data můžou přenášet k účtu úložiště na vyžádání nebo v automaticky naplánovaných intervalech. Další informace o Azure diagnostics najdete v tématu [povolení diagnostiky Azure Cloud Services a virtuálních počítačů](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Stránka Nastavení

Na **nastavení** stránky, nastavení můžete přidat do konfigurace jako dvojice název hodnota. Kód spuštěný v roli může číst hodnoty nastavení konfigurace v době běhu pomocí třídy poskytované [spravované knihovny Azure](http://go.microsoft.com/fwlink?LinkID=171026), konkrétně [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metoda.

### <a name="configuring-a-connection-string-for-a-storage-account"></a>Konfigurace připojovací řetězec pro účet úložiště

Připojovací řetězec je nastavení, která poskytuje informace o připojení a ověřování pro emulátor úložiště nebo pro účet úložiště Azure. Vždy, když kód v roli přistupuje k úložišti Azure (objekty BLOB, fronty nebo tabulky), musí připojovací řetězec.

> [!Note]
> Připojovací řetězec pro účet úložiště Azure, musíte použít definované formát (viz [nakonfigurování připojovacích řetězců úložiště Azure](storage/common/storage-configure-connection-string.md)).

Cloudové služby, můžete nastavit připojovací řetězec k použití místní úložiště, podle potřeby, když nasazujete aplikace nastavte na účet úložiště Azure. Nepodařilo se správně nastavit připojovací řetězec může způsobit vaše role není spuštění a cyklicky přepínat mezi stavy inicializace, zaneprázdněn a ukončení.

Vytvoření připojovacího řetězce, vyberte **přidat nastavení** a nastavte **typ** "Připojovací řetězec".

Pro nové nebo existující připojovací řetězce, vyberte **...** * na pravé straně **hodnotu** pole, které chcete otevřít **vytvoření připojovacího řetězce úložiště** dialogové okno:

1. V části **připojit pomocí**, vyberte **vaše předplatné** možnost zvolit účet úložiště z odběru. Visual Studio pak získá přihlašovací údaje účtu úložiště automaticky z `.publishsettings` souboru.
1. Výběr **ručně zadali přihlašovací údaje** umožňuje zadat název účtu a klíče přímo pomocí informací z portálu Azure. Zkopírovat klíč účtu:. Přejděte na účet úložiště v Azure portálu a vyberte možnost **Správa klíčů**.
    2. Pokud chcete zkopírovat klíč účtu, přejděte na účet úložiště na portálu Azure, vyberte **Nastavení > přístupové klíče**, potom použijte tlačítko Kopírovat, pokud primární přístupový klíč zkopírujte do schránky.
1. Vyberte jednu z možností připojení. **Zadejte vlastní koncové body** požádá, abyste zadali konkrétní adresy URL pro objekty BLOB, tabulek a fronty. Vlastní koncové body umožňují používat [vlastní domény](storage/blobs/storage-custom-domain-name.md) a k řízení přístupu více přesně. V tématu [konfiguraci připojovacích řetězců Azure Storage](./storage/common/storage-configure-connection-string.md).
1. Vyberte **OK**, pak **soubor > Uložit** se aktualizovat konfiguraci s nový připojovací řetězec.

Znovu když publikujete aplikaci do Azure, zvolte konfigurace služby, který obsahuje účet úložiště Azure pro připojovací řetězec. Po publikování aplikace, ověřte, že aplikace funguje podle očekávání proti služby Azure storage.

Další informace o aktualizaci konfigurace služby, najdete v části [spravovat připojovací řetězce pro účty úložiště](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts).

## <a name="endpoints-page"></a>Stránka Koncové body

Webové role má obvykle jeden koncový bod HTTP na portu 80. Roli pracovního procesu na druhé straně může mít libovolný počet koncových bodů protokolu HTTP, HTTPS nebo TCP. Koncové body můžou být vstupních koncových bodů, které jsou k dispozici pro externí klienty, nebo vnitřních koncových bodů, které jsou k dispozici na jiných rolí, které běží ve službě.

- Koncový bod HTTP zpřístupnit do externích klientů a webové prohlížeče, změňte typ koncového bodu jako vstup a zadejte název a číslo veřejného portu.
- Aby byl dostupný koncový bod HTTPS do externích klientů a webových prohlížečů, změnit typ koncového bodu na **vstupní**a zadejte název, číslo veřejného portu a název certifikátu pro správu. Certifikát musí definovat také na **certifikáty** stránka vlastností, abyste mohli zadat certifikát pro správu. 
- Koncový bod zpřístupnit pro interní přístupu podle rolí v rámci cloudové služby, změňte typ koncového bodu na interní a zadejte název a privátní porty možné pro tento koncový bod.

## <a name="local-storage-page"></a>Stránka Místní úložiště

Můžete použít **místní úložiště** stránka vlastností můžete vyhradit jeden nebo více prostředků místní úložiště pro roli. Prostředek Místní úložiště je vyhrazené adresáře v systému souborů Azure virtuálního počítače, ve kterém je spuštěna instance role.

## <a name="certificates-page"></a>Stránka certifikátů

**Certifikáty** stránka vlastností přidá informace o certifikáty konfigurace služby. Všimněte si, že certifikáty nejsou spojených s službou; musíte nahrát certifikáty samostatně do Azure pomocí [portál Azure](http://portal.azure.com).

Přidání certifikátu zde přidá informace o certifikáty do konfigurace služby. Certifikáty nejsou zabalené službou; musíte nahrát certifikáty samostatně prostřednictvím portálu Azure.

Pokud chcete přiřadit certifikát vaše role, zadejte název pro certifikát. Tento název je použít k odkazování na certifikátu, když konfigurujete na koncový bod HTTPS **koncové body** stránky. V dalším kroku určete, zda je úložiště certifikátů **místního počítače** nebo **aktuální uživatel** a název úložiště. Nakonec zadejte kryptografický otisk certifikátu. Pokud je certifikát v aktuální User\Personal (My) úložiště, můžete zadat kryptografický otisk certifikátu výběrem certifikátu z vyplněná seznamu. Pokud se nachází v jiném umístění, zadejte hodnotu kryptografického otisku ručně.

Když přidáte certifikát z úložiště certifikátů, nastavení konfigurace pro vás se automaticky přidají všechny zprostředkující certifikáty. Kromě toho tyto zprostředkující certifikáty musí být nahrán do Azure a správnou konfiguraci služby pro protokol SSL.

Všechny certifikáty správy, které přidružit služby použít k službě jenom v případě, že je spuštěna v cloudu. Pokud vaše služba běží v místní vývojové prostředí, používá standardní certifikát, který je spravován pomocí emulátoru služby výpočty v.
