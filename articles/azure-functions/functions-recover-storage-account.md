---
title: Řešení potíží s Modul runtime služby Azure Functions je nedosažitelné.
description: Přečtěte si, jak řešit potíže s neplatným účtem úložiště.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963882"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Řešení potíží s modulem runtime funkcí je nedosažitelný.

Tento článek je určený k řešení potíží s chybovou zprávou "běhový modul je nedosažitelný", když se zobrazuje v Azure Portal. Pokud k této chybě dojde, zobrazí se na portálu následující chybový řetězec.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

K tomu dochází, když Modul runtime služby Azure Functions nepůjde spustit. Nejběžnějším důvodem této chyby je, že aplikace Function App ztratí přístup k účtu úložiště. Další informace najdete v tématu [požadavky na účet úložiště](storage-considerations.md#storage-account-requirements).

Zbytek tohoto článku vám pomůže vyřešit následující příčiny této chyby, včetně toho, jak identifikovat a vyřešit jednotlivé případy.

+ [Odstraněný účet úložiště](#storage-account-deleted)
+ [Odstraněné nastavení aplikace pro účet úložiště](#storage-account-application-settings-deleted)
+ [Neplatné přihlašovací údaje účtu úložiště](#storage-account-credentials-invalid)
+ [Nedostupný účet úložiště](#storage-account-inaccessible)
+ [Překročena denní kvóta spuštění](#daily-execution-quota-full)
+ [Vaše aplikace je za bránou firewall.](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Účet úložiště se odstranil.

Každá aplikace Function App vyžaduje, aby účet úložiště fungoval. V případě odstranění tohoto účtu nebude funkce fungovat.

### <a name="how-to-find-your-storage-account"></a>Jak najít účet úložiště

Začněte tím, že v nastavení aplikace vyhledáte název svého účtu úložiště. Buď `AzureWebJobsStorage`, nebo `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bude obsahovat název vašeho účtu úložiště zabaleného v připojovacím řetězci. Další informace najdete [tady v referenčních informacích k nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Vyhledejte svůj účet úložiště v Azure Portal a podívejte se, jestli stále existuje. Pokud byla odstraněna, bude nutné znovu vytvořit účet úložiště a nahradit připojovací řetězce úložiště. Váš kód funkce je ztracen a bude nutné ho znovu nasadit.

## <a name="storage-account-application-settings-deleted"></a>Odstranila se nastavení aplikace účtu úložiště.

Pokud jste v předchozím kroku neměli připojovací řetězec účtu úložiště, pravděpodobně jste ho odstranili nebo přepsali. Odstranění nastavení aplikace se obvykle provádí při použití slotů nasazení nebo Azure Resource Manager skriptů k nastavení nastavení aplikace.

### <a name="required-application-settings"></a>Požadovaná nastavení aplikace

* Požaduje se
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Vyžadováno pro funkce plánu spotřeby
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Tady si přečtěte o těchto nastaveních aplikace](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Pokyny

* Pro některá z těchto nastavení nekontrolujte nastavení slotu. Při prohození slotů nasazení se ukončí aplikace Function App.
* Tato nastavení neměňte v rámci automatizovaných nasazení.
* Tato nastavení musí být k dispozici a platná v době vytvoření. Automatizované nasazení, které neobsahuje tato nastavení, má za následek aplikaci Function App, která se nespustí, i když se nastavení přidá později.

## <a name="storage-account-credentials-invalid"></a>Přihlašovací údaje účtu úložiště nejsou platné.

Pokud znovu vygenerujete klíče úložiště, je potřeba aktualizovat připojovací řetězce výše uvedeného účtu úložiště. [Přečtěte si další informace o správě klíčů úložiště tady](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-inaccessible"></a>Účet úložiště není dostupný.

Vaše aplikace Function App musí být schopna získat přístup k účtu úložiště. Mezi běžné problémy, které blokují přístup funkcí k účtu úložiště, patří:

+ aplikace Function App se nasazují do prostředí App Service (pomocného mechanismu) bez správných síťových pravidel, která umožňují provoz do a z účtu úložiště.

+ Brána firewall účtu úložiště je povolená a není nakonfigurovaná tak, aby umožňovala provoz do a z funkcí. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](../storage/common/storage-network-security.md).

## <a name="daily-execution-quota-full"></a>Úplná kvóta spuštění plná

Pokud máte nakonfigurovanou denní kvótu spuštění, aplikace Function App je dočasně zakázaná, což způsobuje, že mnohé z ovládacích prvků portálu nebudou dostupné. 

+ Pokud chcete ověřit [Azure Portal](https://portal.azure.com), otevřete **funkce platformy** > **Function App nastavení** ve vaší aplikaci Function App. Pokud jste nastavili **kvótu denního využití** , kterou nastavíte, zobrazí se tato zpráva:

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ Pokud chcete tento problém vyřešit, odeberte nebo Zvyšte denní kvótu a restartujte aplikaci. V opačném případě bude spuštění aplikace zablokované do dalšího dne.

## <a name="app-is-behind-a-firewall"></a>Aplikace je za bránou firewall.

Pokud je vaše aplikace Functions hostovaná s [interním vyrovnáváním zatížení App Service Environment](../app-service/environment/create-ilb-ase.md) a je nakonfigurovaná tak, aby blokovala příchozí internetový provoz, nebo má nakonfigurovaná [omezení příchozích IP adres](functions-networking-options.md#inbound-ip-restrictions) pro blokování přístupu k Internetu, nebude váš modul runtime vaší funkce dostupný. Azure Portal volá přímo do spuštěné aplikace, aby načetla seznam funkcí a také prostřednictvím volání HTTP do koncového bodu KUDU. Nastavení na úrovni platformy na kartě `Platform Features` bude stále k dispozici.

Pokud chcete ověřit konfiguraci pomocného programu pro čtení, přejděte k NSG podsítě, kde se nachází Správce služby, a ověřte příchozí pravidla, aby se povolil provoz z veřejné IP adresy počítače, na který aplikaci přistupuje. Portál můžete použít taky z počítače připojeného k virtuální síti, na které běží vaše aplikace, nebo na virtuálním počítači spuštěném ve virtuální síti. [Tady si můžete přečíst další informace o konfiguraci příchozího pravidla.](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Další kroky

Seznamte se s monitorováním aplikací Function App:

> [!div class="nextstepaction"]
> [Azure Functions monitorování](functions-monitoring.md)
