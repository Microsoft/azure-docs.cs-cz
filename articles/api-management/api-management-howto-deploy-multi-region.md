---
title: Nasazení služby Azure API Management do několika oblastí Azure | Dokumentace Microsoftu
description: Zjistěte, jak můžete nasadit instanci služby Azure API Management do několika oblastí Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: apimpm
ms.openlocfilehash: 27bfd3176ecad847f9bba2a62abd66b55484443b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443011"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Jak nasadit instanci služby Azure API Management do několika oblastí Azure

Azure API Management podporuje nasazení v různých oblastech, která umožňuje vydavatelé rozhraní API k distribuci jeden služba Azure API management napříč libovolným počtem požadované oblasti Azure. To pomáhá snížit žádost latence vnímané geograficky distribuovanou zákazníky a také zlepšuje dostupnost služeb, pokud jedna oblast přejde do režimu offline.

Nová služba Azure API Management zpočátku obsahuje pouze jeden [jednotky] [ unit] v jedné oblasti Azure, primární oblasti. Na webu Azure portal můžete snadno přidány další oblasti. Server brány služby API Management je nasazené do každé oblasti a volání provoz se směruje do nejbližší brány. Pokud oblast přejde do režimu offline, provoz automaticky přesměruje na další bránu co nejblíž koncovým.

> [!NOTE]
> Azure API Management replikuje pouze komponenty brány rozhraní API napříč oblastmi. Součást služby správy hostuje jenom v primární oblasti. I v případě výpadku v primární oblasti provádění změn konfigurace do instance služby Azure API Management se nedá – včetně nastavení nebo aktualizace zásady.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Nasaďte instanci služby API Management do nové oblasti

> [!NOTE]
> Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance služby API Management][Create an API Management service instance].

Na webu Azure Portal, přejděte **škálování a ceny** stránky vaší instance služby API Management. 

![Karta škálování][api-management-scale-service]

Pokud chcete nasadit do nové oblasti, klikněte na **+ přidat oblast** z panelu nástrojů.

![Přidat oblast][api-management-add-region]

Z rozevíracího seznamu vyberte umístění a nastavit počet jednotek pomocí posuvníku.

![Zadejte jednotky][api-management-select-location-units]

Klikněte na tlačítko **přidat** umístit svůj výběr v tabulce umístění. 

Tento postup opakujte, dokud nebudete mít všechny umístění nakonfigurovaná a klikněte na tlačítko **Uložit** z panelu nástrojů k zahájení procesu nasazení.

## <a name="remove-region"> </a>Odstranit z umístění instance služby API Management

Na webu Azure Portal, přejděte **škálování a ceny** stránky vaší instance služby API Management. 

![Karta škálování][api-management-scale-service]

Pro umístění, které chcete odebrat, otevřete pomocí místní nabídky **...**  tlačítko na pravém konci tabulky. Vyberte **odstranit** možnost.

Potvrzení odstranění a klikněte na tlačítko **Uložit** změny se projeví.

## <a name="route-backend"> </a>Trasy rozhraní API volání služeb místní back-endu

Azure API Management obsahuje pouze jednu adresu URL back-end služby. I když jsou instance Azure API Management v různých oblastech, brána rozhraní API bude stále směrovala požadavky do stejného back-end službu, která je nasazena v pouze jedna oblast. V tomto případě se výkonový zisk plynoucí pocházejí pouze z odpovědí do mezipaměti v rámci Azure API Management v oblasti specifické pro žádosti, ale kontaktování back-endu po celém světě může stále způsobovat tak vysokou latenci.

Plně využívat geografické distribuce systému, měli byste mít back-endových služeb, které jsou nasazené ve stejné oblasti jako instance Azure API Management. Potom pomocí zásad a `@(context.Deployment.Region)` vlastnost, můžete směrovat provoz do místní instance back-endu.

1. Přejděte k vaší instanci Azure API Management a klikněte na **rozhraní API** v levé nabídce.
2. Vyberte požadované rozhraní API.
3. Klikněte na tlačítko **editor kódu** z šipku rozevíracího seznamu v **zpracování příchozích**.

    ![Editor kódu rozhraní API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Použití `set-backend` v kombinaci s podmíněné `choose` zásady k sestavení kompletních správné zásady směrování v `<inbound> </inbound>` část souboru.

    Například níže XML soubor bude fungovat pro oblasti západní USA a východní Asie:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/
