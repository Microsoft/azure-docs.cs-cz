---
title: 'Chyba při potížích: Azure Functions Runtime je nedostupný'
description: Přečtěte si, jak řešit potíže s neplatným účtem úložiště.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063777"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Chyba při potížích: "Azure Functions Runtime je nedostupný"

Tento článek vám pomůže vyřešit následující chybový řetězec, který se zobrazí na webu Azure Portal:

> "Chyba: Azure Functions Runtime je nedostupný. Klikněte zde pro podrobnosti o konfiguraci úložiště."

K tomuto problému dochází, když azure functions runtime nelze spustit. Nejčastějším důvodem problému je, že aplikace funkce ztratila přístup ke svému účtu úložiště. Další informace naleznete v tématu [Požadavky na účet úložiště](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

Zbývající část tohoto článku vám pomůže vyřešit následující příčiny této chyby, včetně jak identifikovat a vyřešit každý případ.

## <a name="storage-account-was-deleted"></a>Účet úložiště byl odstraněn.

Každá aplikace funkce vyžaduje účet úložiště pro provoz. Pokud je tento účet odstraněn, vaše funkce nebude fungovat.

Začněte tak, že v nastavení aplikace najdete název účtu úložiště. Buď `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` název účtu úložiště zabalené do připojovacího řetězce. Další informace najdete [v tématu Odkaz na nastavení aplikace pro Funkce Azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Vyhledejte svůj účet úložiště na webu Azure Portal a zjistěte, jestli ještě existuje. Pokud byl odstraněn, znovu vytvořte účet úložiště a nahraďte připojovací řetězce úložiště. Kód funkce je ztracen a je třeba jej znovu nasadit.

## <a name="storage-account-application-settings-were-deleted"></a>Nastavení aplikace účtu úložiště bylo odstraněno.

V předchozím kroku, pokud nemůžete najít připojovací řetězec účtu úložiště, byl pravděpodobně odstraněn nebo přepsán. K odstranění nastavení aplikace nejčastěji dochází, když k nastavení aplikací nastavujete sloty nasazení nebo skripty Azure Resource Manageru.

### <a name="required-application-settings"></a>Požadované nastavení aplikace

* Požadované:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Požadováno pro funkce plánu spotřeby:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Další informace najdete [v tématu Odkaz na nastavení aplikace pro Funkce Azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Doprovodné materiály

* Nekontrolujte "nastavení slotu" pro žádné z těchto nastavení. Pokud zaměníte sloty nasazení, aplikace funkce se přeruší.
* Tato nastavení neupravujte jako součást automatizovaných nasazení.
* Tato nastavení musí být poskytnuta a platná v době vytvoření. Automatické nasazení, které neobsahuje tato nastavení, má za následek aplikaci funkcí, která se nespustí, i když jsou nastavení přidána později.

## <a name="storage-account-credentials-are-invalid"></a>Pověření účtu úložiště jsou neplatná.

Dříve popisované připojovací řetězce účtu úložiště musí být aktualizovány, pokud znovu vygenerujete klíče úložiště. Další informace o správě klíčů úložiště najdete [v tématu Vytvoření účtu úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>Účet úložiště je nepřístupný

Vaše aplikace funkce musí mít přístup k účtu úložiště. Běžné problémy, které blokují přístup aplikace pro funkci k účtu úložiště, jsou:

* Aplikace funkce se nasadí do prostředí služby App Service bez správných síťových pravidel, která umožňují přenos do a z účtu úložiště.

* Brána firewall účtu úložiště je povolena a není nakonfigurována tak, aby umožňovala přenos y do a z funkcí. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>Denní kvóta provádění je plná.

Pokud máte nakonfigurovanou kvótu denního spuštění, aplikace funkce je dočasně zakázána, což způsobí, že mnoho ovládacích prvků portálu přestane být k dispozici. 

Pokud chcete ověřit kvótu na [portálu Azure](https://portal.azure.com), vyberte**nastavení aplikací** **Funkce** > funkce platformy ve vaší aplikaci funkce. Pokud jste překročili **nastavenou kvótu denního využití,** zobrazí se následující zpráva:

  > "Aplikace funkce dosáhla denní kvóty využití a byla zastavena až do následujícího časového rámce 24 hodin."

Chcete-li tento problém vyřešit, odeberte nebo zvyšte denní kvótu a restartujte aplikaci. V opačném případě je spuštění aplikace blokováno až do následujícího dne.

## <a name="app-is-behind-a-firewall"></a>Aplikace je za firewallem

Běh funkce může být nedostupný z některého z následujících důvodů:

* Vaše aplikace funkcí je hostována v [prostředí služby App Service s vyrovnáváním zatížení](../app-service/environment/create-ilb-ase.md) a je nakonfigurovaná tak, aby blokovala příchozí internetový provoz.

* Vaše aplikace funkce má [příchozí omezení IP adres,](functions-networking-options.md#inbound-ip-restrictions) které jsou nakonfigurovány tak, aby blokovaly přístup k internetu. 

Portál Azure provádí volání přímo do spuštěné aplikace k načtení seznamu funkcí a provádí volání HTTP do koncového bodu Kudu. Nastavení na úrovni **platformy** na kartě Funkce platformy jsou stále k dispozici.

Ověření konfigurace prostředí služby App Service:
1. Přejděte do skupiny zabezpečení sítě (NSG) podsítě, kde se nachází prostředí služby App Service.
1. Ověřte příchozí pravidla, abyste povolili provoz přicházející z veřejné IP adresy počítače, ve kterém přistupujete k aplikaci. 
   
Portál můžete taky použít z počítače, který je připojený k virtuální síti, ve které je spuštěná vaše aplikace, nebo k virtuálnímu počítači, který běží ve vaší virtuální síti. 

Další informace o konfiguraci příchozích pravidel naleznete v části Skupiny zabezpečení sítě [v části Aspekty sítě pro prostředí služby App Service](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups).

## <a name="next-steps"></a>Další kroky

Další informace o sledování funkčních aplikací:

> [!div class="nextstepaction"]
> [Monitorování Azure Functions](functions-monitoring.md)
