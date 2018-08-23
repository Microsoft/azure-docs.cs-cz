---
title: Konfigurace projektu Azure použití několika konfigurací služby | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat projekt cloudové služby Azure tak, že změníte soubor ServiceDefinition.csdef ServiceConfiguration.Local.cscfg a ServiceConfiguration.Cloud.cscfg soubory.
services: visual-studio-online
author: ghogen
manager: douge
assetId: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 56b01855a5996bb62f721bda2bfc207684227854
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056605"
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>Konfigurace projektu Azure v sadě Visual Studio používat více konfigurace služby

Projektu cloudové služby Azure v sadě Visual Studio obsahuje tři konfigurační soubory: `ServiceDefinition.csdef`, `ServiceConfiguration.Local.cscfg`, a `ServiceConfiguration.Cloud.cscfg`:

- `ServiceDefinition.csdef` nasazení do Azure k popisu požadavků cloudové službě a její role a k poskytování nastavení, která se vztahují na všechny instance. Nastavení lze číst za běhu pomocí rozhraní API služeb Azure hostování modulu Runtime. Tento soubor je aktualizovat v Azure, pouze v případě, že Cloudová služba je zastavena.
- `ServiceConfiguration.Local.cscfg` a `ServiceConfiguration.Cloud.cscfg` zadejte hodnoty pro nastavení v definici souboru a zadejte počet instancí se mají spustit pro každou roli. "Local" soubor obsahuje hodnoty použité v místním ladění. soubor "Cloud" je nasadit do Azure jako `ServiceConfiguration.cscfg` a poskytuje nastavení pro prostředí serveru. Tento soubor můžete aktualizovat, pokud vaše Cloudová služba je spuštěná v Azure.

Nastavení konfigurace jsou spravovaná a upravit v sadě Visual Studio pomocí stránky vlastností pro příslušné roli (pravým tlačítkem myši na roli a vyberte **vlastnosti**, nebo dvakrát klikněte na roli). Změny se dají vymezit na podle toho, která konfigurace je vybrán v **konfigurace služby** rozevíracího seznamu. Vlastnosti pro webové a pracovní role jsou podobné, s výjimkou tam, kde je popsáno v následujících částech.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Informace o základní schémata pro definici služby a služby konfigurační soubory, najdete v článku [.csdef XML schématu](cloud-services/schema-csdef-file.md) a [.cscfg XML schématu](cloud-services/schema-cscfg-file.md) článků. Další informace o konfiguraci služby najdete v tématu [jak konfigurovat Cloud Services](cloud-services/cloud-services-how-to-configure-portal.md).


## <a name="configuration-page"></a>Stránka Konfigurace

### <a name="service-configuration"></a>Konfigurace služby

Vybere, které `ServiceConfiguration.*.cscfg` souboru je ovlivněny změnami. Ve výchozím nastavení, jsou místní a cloudové variant a můžete použít **spravovat...**  příkaz Kopírovat, přejmenovat a odstranit konfigurační soubory. Tyto soubory jsou přidány do projektu cloudové služby a zobrazí v **Průzkumníka řešení**. Ale přejmenování nebo odstranění konfigurace lze provést pouze z tohoto ovládacího prvku.

### <a name="instances"></a>Instance

Nastavte **Instance** počet na počet instancí služby by měl spustit pro tuto roli.

Nastavte **velikost virtuálního počítače** vlastnost **nejmenším instancím**, **malé**, **střední**, **velké**, nebo **Velmi velká**.  Další informace najdete v tématu [velikosti pro Cloud Services](cloud-services/cloud-services-sizes-specs.md).

### <a name="startup-action-web-role-only"></a>Spuštění akcí (jenom webové role)

Nastavení této vlastnosti a určit tak, že by měl Visual Studio při spuštění ladění spusťte webový prohlížeč pro koncové body HTTP nebo koncové body HTTPS nebo obojí.

**Koncový bod HTTPS** možnost je dostupná jenom v případě, že jsou již definovány koncový bod HTTPS pro vaši roli. Můžete definovat koncový bod HTTPS na **koncové body** stránku vlastností.

Pokud jste už přidali koncový bod HTTPS, je ve výchozím nastavení povolena možnost koncový bod HTTPS a spustí prohlížeč pro tento koncový bod aplikace Visual Studio při spuštění ladění, kromě prohlížeče pro vaše koncové body HTTP, za předpokladu, že jsou povoleny obě možnosti spuštění.

### <a name="diagnostics"></a>Diagnostika

Ve výchozím nastavení je povolená Diagnostika pro webovou roli. Účet služby Azure cloud projektu a úložiště jsou nastaveny na použití emulátoru lokálního úložiště. Až budete připravení nasadit do Azure, můžete vybrat tlačítko Tvůrce (**...** ) místo toho použít Azure storage. Diagnostická data můžou přenášet do účtu úložiště na vyžádání nebo automaticky plánovaných intervalech. Další informace o diagnostice Azure najdete v tématu [povolení diagnostiky v Azure Cloud Services a Virtual Machines](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Stránka Nastavení

Na **nastavení** stránky, můžete přidat nastavení konfigurace jako dvojice název hodnota. Kód spuštěný v roli můžete číst hodnoty nastavení konfigurace v době běhu pomocí tříd poskytovaných oborem [spravované knihovny Azure](http://go.microsoft.com/fwlink?LinkID=171026), konkrétně [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metoda.

### <a name="configuring-a-connection-string-for-a-storage-account"></a>Konfigurace připojovacího řetězce pro účet úložiště

Připojovací řetězec je nastavení, která poskytuje informace o připojení a ověřování pro emulátor úložiště nebo pro účet úložiště Azure. Pokaždé, když kód v roli přistupuje k Azure storage (objekty BLOB, fronty nebo tabulky), musí připojovací řetězec.

> [!Note]
> Připojovací řetězec pro účet úložiště Azure, musíte použít definovaném formátu (viz [Konfigurace připojovacích řetězců úložiště Azure](storage/common/storage-configure-connection-string.md)).

Můžete nastavit připojovací řetězec má použít místní úložiště, podle potřeby, když nasazujete aplikace nastavte na účet úložiště Azure, cloudové službě. Nepodařilo se nastavit připojovací řetězec správně může způsobit vaše role spustit, nebo k cyklování skrze stavy inicializace, zaneprázdněný a zastavuje.

Vytvoření připojovacího řetězce, vyberte **přidat nastavení** a nastavte **typ** "Připojovací řetězec".

Pro nové nebo existující připojovací řetězce, vyberte **...** * na pravé straně **hodnotu** pole, které chcete otevřít **vytvořit připojovací řetězec úložiště** dialogové okno:

1. V části **připojit pomocí**, zvolte **předplatného** možnost vybrat účet úložiště z předplatného. Visual Studio pak získá přihlašovací údaje účtu úložiště automaticky z `.publishsettings` souboru.
1. Výběr **ručně zadali přihlašovací údaje** umožňuje zadat název účtu a klíč přímo pomocí informací na webu Azure Portal. Zkopírování klíče účtu:. Přejděte na účet úložiště na Azure portal a vyberte **Správa klíčů**.
    2. Pokud chcete zkopírovat klíč účtu, přejděte na účet úložiště na webu Azure portal vyberte **Nastavení > přístupové klíče**, potom pomocí tlačítka pro kopírování zkopírujte primární přístupový klíč do schránky.
1. Vyberte jednu z možností připojení. **Zadejte vlastní koncové body** se zeptá, abyste zadali konkrétní adresy URL pro objekty BLOB, tabulky a zařadí do fronty. Vlastní koncové body umožňují jeho používání [vlastních domén](storage/blobs/storage-custom-domain-name.md) a řízení přístupu k více přesně. Zobrazit [nakonfigurování připojovacích řetězců Azure Storage](./storage/common/storage-configure-connection-string.md).
1. Vyberte **OK**, pak **soubor > Uložit** se aktualizovat konfiguraci s nový připojovací řetězec.

Při publikování aplikace do Azure, znovu, vyberte konfiguraci služby, které obsahuje připojovací řetězec účtu úložiště Azure. Po publikování aplikace ověřte, že aplikace funguje podle očekávání pro služby Azure storage.

Další informace o tom, jak aktualizovat konfigurace služby, najdete v části [spravovat připojovací řetězce pro účty úložiště](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts).

## <a name="endpoints-page"></a>Stránka Koncové body

Webová role obvykle obsahuje jeden koncový bod protokolu HTTP na portu 80. Role pracovního procesu, na druhé straně může mít libovolný počet koncových bodů HTTP, HTTPS nebo TCP. Koncové body můžou být vstupní koncové body, které jsou k dispozici do externích klientů, nebo vnitřní koncové body, které jsou k dispozici pro jiné role, na kterých běží ve službě.

- Pro zpřístupnění koncový bod HTTP do externích klientů a webové prohlížeče, změňte typ koncových bodů na vstup a zadejte název a číslo veřejného portu.
- Pokud chcete zpřístupnit koncového bodu HTTPS do externích klientů a webové prohlížeče, změňte typ koncových bodů na **vstupní**a zadejte název, číslo veřejného portu a název certifikátu správy. Certifikátu musíte také definovat v **certifikáty** stránky vlastností, abyste mohli zadat certifikát pro správu. 
- Aby bylo koncový bod interního přístupu pomocí jiné role v cloudové službě, změňte typ koncových bodů na interní a zadejte název a je to možné privátních portů pro tento koncový bod.

## <a name="local-storage-page"></a>Stránka Místní úložiště

Můžete použít **místní úložiště** stránku vlastností vyhradit jeden nebo více prostředků pro roli místního úložiště. Místní úložiště prostředků je vyhrazené adresáře v systému souborů v Azure virtuální počítač, ve kterém je spuštěna instance role.

## <a name="certificates-page"></a>Stránka certifikátů

**Certifikáty** stránku vlastností přidá informace o certifikátech pro vaše konfigurace služby. Všimněte si, že vaše certifikáty nejsou zabalené službou; musíte nahrát certifikáty samostatně k Azure prostřednictvím [webu Azure portal](http://portal.azure.com).

Přidání certifikátu tady přidá informace o certifikátech pro vaše konfigurace služby. Certifikáty nejsou zabalené službou; musíte nahrát certifikáty samostatně prostřednictvím webu Azure portal.

Pokud chcete přidružit certifikát vaší role, zadejte název certifikátu. Použijte tento název k odkazování na certifikátu, když konfigurujete koncový bod HTTPS na **koncové body** stránky. Dále určete, zda je do úložiště certifikátů **místního počítače** nebo **aktuálního uživatele** a název úložiště. Nakonec zadejte kryptografický otisk certifikátu. Pokud je certifikát v aktuální User\Personal úložiště (Moje), můžete zadat kryptografický otisk certifikátu tak, že vyberete certifikát naplněný seznam. Pokud se nachází v jiném umístění, zadejte hodnotu kryptografického otisku ručně.

Po přidání certifikátu z úložiště certifikátů všechny zprostředkující certifikáty jsou automaticky přidány do konfigurace nastavení za vás. Kromě toho tyto zprostředkující certifikáty, musí se nahrát do Azure a správnou konfiguraci služby pro protokol SSL.

Všechny certifikáty pro správu, které spojují s vaší službou použijte k vaší službě pouze v případě, že je spuštěná v cloudu. Pokud vaše služba je spuštěná v místním vývojovém prostředí, používá standardní certifikát, který je spravovaný nástrojem emulátor služby výpočty.
