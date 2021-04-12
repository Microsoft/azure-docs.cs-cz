---
title: 'Chyba řešení potíží: Modul runtime služby Azure Functions je nedosažitelný'
description: Přečtěte si, jak řešit potíže s neplatným účtem úložiště.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 392882fc2f0394e61aee973a20479d8f1fa9bc92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606969"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Chyba řešení potíží: "Modul runtime služby Azure Functions je nedosažitelný"

Tento článek vám pomůže při odstraňování potíží s následujícím chybovým řetězcem, který se zobrazí v Azure Portal:

> "Chyba: Modul runtime služby Azure Functions je nedosažitelný. Kliknutím sem zobrazíte podrobnosti o konfiguraci úložiště. "

K tomuto problému dochází, když modul runtime Functions nemůže být spuštěn. Nejběžnějším důvodem je to, že aplikace Function App ztratila přístup k účtu úložiště. Další informace najdete v tématu [požadavky na účet úložiště](storage-considerations.md#storage-account-requirements).

Zbytek tohoto článku vám pomůže vyřešit konkrétní příčiny této chyby, včetně toho, jak identifikovat a vyřešit jednotlivé případy.

## <a name="storage-account-was-deleted"></a>Účet úložiště se odstranil.

Každá aplikace Function App vyžaduje, aby účet úložiště fungoval. Pokud se tento účet odstraní, vaše funkce nebudou fungovat.

Začněte tím, že v nastavení aplikace vyhledáte název svého účtu úložiště. Buď `AzureWebJobsStorage` nebo `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` obsahuje název účtu úložiště jako součást připojovacího řetězce. Další informace najdete v tématu [referenční informace k nastavení aplikace Azure Functions](./functions-app-settings.md#azurewebjobsstorage).

V Azure Portal vyhledejte svůj účet úložiště, abyste viděli, jestli stále existuje. Pokud byla odstraněna, znovu vytvořte účet úložiště a nahraďte připojovací řetězce úložiště. Váš kód funkce je ztracen a je nutné ho znovu nasadit.

## <a name="storage-account-application-settings-were-deleted"></a>Odstranila se nastavení aplikace účtu úložiště.

Pokud v předchozím kroku nemůžete najít připojovací řetězec účtu úložiště, byl pravděpodobně odstraněn nebo přepsán. K nastavení aplikace se nejčastěji dochází při použití slotů nasazení nebo Azure Resource Manager skriptů k nastavení nastavení aplikace.

### <a name="required-application-settings"></a>Požadovaná nastavení aplikace

* Požadovanou
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* Vyžaduje se pro funkce plánu Premium:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

Další informace najdete v tématu [referenční informace k nastavení aplikace Azure Functions](./functions-app-settings.md).

### <a name="guidance"></a>Pokyny

* Pro některá z těchto nastavení nekontrolujte **nastavení slotu** . Pokud odměňujete sloty nasazení, aplikace Function App se ukončí.
* Tato nastavení neměňte v rámci automatizovaných nasazení.
* Tato nastavení musí být k dispozici a platná v době vytvoření. Automatizované nasazení, které neobsahuje tato nastavení, má za následek aplikaci Function App, která se nespustí, i když se nastavení přidá později.

## <a name="storage-account-credentials-are-invalid"></a>Přihlašovací údaje účtu úložiště nejsou platné.

V případě, že znovu vygenerujete klíče úložiště, je třeba aktualizovat dříve popsané připojovací řetězce účtu úložiště. Další informace o správě klíčů úložiště najdete v tématu [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md).

## <a name="storage-account-is-inaccessible"></a>Účet úložiště není dostupný.

Vaše aplikace Function App musí být schopna získat přístup k účtu úložiště. Mezi běžné problémy, které blokují přístup aplikace Function App k účtu úložiště, patří:

* Aplikace Function App se nasadí do vašeho App Service Environment (pomocného mechanismu) bez správných síťových pravidel, která umožňují provoz do a z účtu úložiště.

* Brána firewall účtu úložiště je povolená a není nakonfigurovaná tak, aby umožňovala provoz do a z funkcí. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
* Ověřte, že `allowSharedKeyAccess` nastavení je nastaveno na `true` výchozí hodnotu. Další informace najdete v tématu [zabránění autorizaci sdíleného klíče pro účet Azure Storage](https://docs.microsoft.com/azure/storage/common/shared-key-authorization-prevent?tabs=portal#verify-that-shared-key-access-is-not-allowed). 

## <a name="daily-execution-quota-is-full"></a>Kvóta denního spuštění je plná.

Pokud máte nakonfigurovanou denní kvótu spuštění, aplikace Function App je dočasně zakázaná, což způsobuje, že mnohé z ovládacích prvků portálu nebudou dostupné. 

Pokud chcete ověřit kvótu v [Azure Portal](https://portal.azure.com), vyberte **funkce platformy**  >  **Function App nastavení** ve vaší aplikaci Function App. Pokud používáte **kvótu denního využití** , kterou jste nastavili, zobrazí se následující zpráva:

  > "Function Appa dosáhla kvóty denního využití a byla zastavena až do dalších 24 hodin časového rámce."

Pokud chcete tento problém vyřešit, odeberte nebo Zvyšte denní kvótu a pak aplikaci restartujte. V opačném případě bude spuštění aplikace zablokované do dalšího dne.

## <a name="app-is-behind-a-firewall"></a>Aplikace je za bránou firewall.

Aplikace Function App může být nedosažitelná z některého z následujících důvodů:

* Vaše aplikace Function App je hostovaná [interně s vyrovnáváním zatížení App Service Environment](../app-service/environment/create-ilb-ase.md) a je nakonfigurovaná tak, aby blokovala příchozí internetový provoz.

* Vaše aplikace Function App má [omezení příchozích IP adres](functions-networking-options.md#inbound-access-restrictions) , která jsou nakonfigurovaná tak, aby blokovala přístup k Internetu. 

Azure Portal volá přímo do spuštěné aplikace a načte seznam funkcí a provede volání HTTP do koncového bodu Kudu. Nastavení na úrovni platformy na kartě **funkce platformy** jsou stále k dispozici.

Ověření konfigurace pomocného mechanismu:
1. Přejdete do skupiny zabezpečení sítě (NSG) podsítě, ve které se nachází pomocným mechanismem řízení.
1. Ověřte příchozí pravidla, aby povolovala přenosy přicházející z veřejné IP adresy počítače, na který přistupujete k aplikaci. 
   
Portál můžete použít taky z počítače připojeného k virtuální síti, na které běží vaše aplikace, nebo na virtuálním počítači, který běží ve vaší virtuální síti. 

Další informace o konfiguraci příchozího pravidla najdete v části "skupiny zabezpečení sítě" v tématu [požadavky na síť pro App Service Environment](../app-service/environment/network-info.md#network-security-groups).

## <a name="next-steps"></a>Další kroky

Seznamte se s monitorováním aplikací Function App:

> [!div class="nextstepaction"]
> [Monitorování Azure Functions](functions-monitoring.md)
