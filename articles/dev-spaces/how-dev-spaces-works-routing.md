---
title: Jak funguje směrování s Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: V této části najdete popis procesů, které Azure Dev Spaces výkonu a způsobu fungování směrování.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91977965"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Jak funguje směrování s Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces poskytuje několik způsobů, jak rychle iterovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Azure Kubernetes Service (AKS). Po spuštění projektu ve vývojovém prostoru Azure Dev Spaces poskytuje další možnosti sítě a směrování pro váš projekt.

Tento článek popisuje, jak funguje směrování s využitím vývojových prostorů.

## <a name="how-routing-works"></a>Jak funguje směrování

Vývojové místo je postavené na AKS a používá stejné [Koncepty sítě](../aks/concepts-network.md). Azure Dev Spaces má taky centralizovanou službu *ingressmanager* a nasadí svůj vlastní kontroler příchozího přenosu do clusteru AKS. Služba *ingressmanager* monitoruje clustery AKS pomocí vývojových prostorů a rozšiřuje Azure dev Spaces řadič příchozího přenosu v clusteru s objekty příchozího přenosu dat pro směrování do lusků aplikací. Kontejner devspaces-proxy v každém z nich přidá `azds-route-as` hlavičku HTTP pro přenosy HTTP do vývojového prostoru založeného na adrese URL. Například požadavek na adresu URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* získá HLAVIČKU http s `azds-route-as: azureuser` . Kontejner devspaces-proxy nebude přidávat `azds-route-as` hlavičku, pokud již existuje.

Když se ve službě mimo cluster provede požadavek HTTP, požadavek přejde do kontroleru příchozího přenosu dat. Kontroler příchozího přenosu trasuje požadavek přímo na příslušné pole na základě jeho objektů a pravidel příchozího přenosu dat. Kontejner devspaces-proxy v rámci obdrží požadavek, přidá `azds-route-as` hlavičku na základě adresy URL a potom směruje požadavek do kontejneru aplikace.

Pokud se ve službě v rámci clusteru provede požadavek HTTP služby z jiné služby, požadavek se nejprve doprovází do kontejneru devspaces-proxy služby volající služby. Kontejner devspaces-proxy vyhledává požadavek HTTP a kontroluje `azds-route-as` hlavičku. V závislosti na záhlaví kontejner devspaces-proxy vyhledá IP adresu služby přidružené k hodnotě hlavičky. Pokud je nalezena IP adresa, kontejner devspaces-proxy přesměruje požadavek na tuto IP adresu. Pokud se IP adresa nenajde, kontejner devspaces-proxy směruje požadavek do nadřazeného kontejneru aplikace.

Například *Služba* Applications a *serviceB* jsou nasazeny do nadřazeného vývojového prostoru s názvem *Default*. *služba Service* spoléhá na *serviceB* a provede volání http. Uživatel Azure vytvoří podřízený prostor pro vývoj založený na *výchozím* prostoru s názvem *azureuser*. Uživatel Azure také nasadí svou vlastní verzi *služby* do jejich podřízeného prostoru. Když se zavede požadavek *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Směrování Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Kontroler příchozího přenosu dat vyhledá adresu pod adresou URL, která je přidružená k adrese URL, která je *Service. azureuser*.
1. Kontroler příchozího přenosu dat vyhledá na zařízení pod vývojovým místem uživatele Azure a směruje požadavek do *služby Service. azureuser* pod.
1. Kontejner devspaces-proxy v rámci *služby Service. azureuser* pod ní obdrží požadavek a přidá ho `azds-route-as: azureuser` jako hlavičku HTTP.
1. Kontejner devspaces-proxy v nástroji *Service. azureuser* pod směruje požadavek do kontejneru Application aplikace *služby* v této *službě. azureuser* pod.
1. Aplikace *služby* v nástroji *Service. Azureuser* pod volá *serviceB*. Aplikace *služby* také obsahuje kód pro zachování existující `azds-route-as` hlavičky, v tomto případě je to `azds-route-as: azureuser` .
1. Kontejner devspaces-proxy v *Service. azureuser* pod obdrží požadavek a VYHLEDÁ IP adresu *serviceB* na základě hodnoty `azds-route-as` záhlaví.
1. Kontejner devspaces-proxy v nástroji *Service. azureuser* pod nenalezne IP adresu pro *serviceB. azureuser*.
1. Kontejner devspaces-proxy v rámci *služby Service. azureuser* vyhledá v nadřazeném prostoru IP adresu *serviceB* , která je *serviceB. Default*.
1. Kontejner devspaces-proxy v nástroji *Service. azureuser* NALEZNE IP adresu pro *serviceB. Default* a směruje požadavek do *serviceB. Default* pod.
1. Kontejner devspaces-proxy v *serviceB. Default* pod obdrží požadavek a směruje požadavek do kontejneru aplikace *serviceB* v *serviceB. Default* pod.
1. Aplikace *serviceB* ve *serviceB. Default* pod vrátí odpověď na *službu Service. azureuser* pod.
1. Kontejner devspaces-proxy v *Service. azureuser* pod obdrží odpověď a směruje odpověď do kontejneru aplikace *služby* v nástroji *Service. azureuser* pod.
1. Aplikace *služby* obdrží odpověď a potom vrátí svoji vlastní odpověď.
1. Kontejner devspaces-proxy v rámci *služby Service. azureuser* pod obdrží odpověď z kontejneru aplikace *služby* a směruje odpověď původnímu volajícímu mimo cluster.

Všechny ostatní přenosy TCP, které neprošly protokolem HTTP, prostřednictvím kontroleru příchozího přenosu dat a kontejnerů devspaces-proxy se nezměnily.

## <a name="sharing-a-dev-space"></a>Sdílení prostoru pro vývoj

Při práci s týmem můžete sdílet vývojové místo v celém týmu a vytvářet odvozené vývojové prostory. Místo pro vývoj může použít kdokoli s přístupem přispěvatele ke skupině prostředků vývojového prostoru.

Můžete také vytvořit nové vývojové místo, které je odvozeno z jiného vývojového prostoru. Při vytváření odvozeného vývojového prostoru je popisek *azds.IO/Parent-Space=Parent-Space-Name* přidán do oboru názvů odvozeného vývojového prostoru. Všechny aplikace z nadřazeného vývojového prostoru jsou také sdíleny s odvozeným místem pro vývoj. Pokud nasadíte aktualizovanou verzi aplikace do odvozeného vývojového prostoru, bude existovat pouze v odvozeném vývojovém prostoru a nadřazené vývojové místo zůstane neovlivněno. Můžete mít maximálně tři úrovně odvozených vývojových prostorů nebo *prarodičů* .

Odvozené místo pro vývoj také inteligentně směruje požadavky mezi vlastní aplikace a aplikace sdílené z jejího nadřazeného objektu. Směrování funguje tak, že se pokusí směrovat požadavek do aplikace v odvozeném vývojovém prostoru a vrátit se do sdílené aplikace z nadřazeného vývojového prostoru. Směrování se vrátí do sdílené aplikace v prostoru No, pokud aplikace není v nadřazeném prostoru.

Například:
* *Výchozí* místo pro vývoj má aplikace *Service* a *serviceB*.
* Vývojové místo *azureuser* je odvozeno od *výchozího nastavení*.
* Do *azureuser* se nasadí aktualizovaná verze *služby Service* .

Při použití *azureuser* budou všechny požadavky na *službu* směrovány do aktualizované verze v *azureuser*. Požadavek na *serviceB* se nejprve pokusí o směrování na *azureuser* verzi *serviceB*. Protože neexistuje, bude směrována do *výchozí* verze *serviceB*. Pokud je odebrána verze *služby* *azureuser* , všechny požadavky na *službu* se vrátí k používání *výchozí* verze *služby*.

## <a name="next-steps"></a>Další kroky

Příklad toho, jak Azure Dev Spaces používá směrování k zajištění rychlé iterace a vývoje, najdete v tématu [jak vzdálené ladění kódu pomocí Azure dev Spaces funguje][how-it-works-remote-debugging].


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md