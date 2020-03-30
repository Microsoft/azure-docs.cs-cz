---
title: Připojovací řetězec úložiště bitových propojení Azure Service Fabric
description: Další informace o připojovací řetězec úložiště bitových obrázků, včetně jeho použití a aplikací pro cluster Service Fabric.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: c3395248188c2a16736cfc8cea262fe163a6944b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645663"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Principy nastavení ImageStoreConnectionString

V některých našich dokumentací stručně zmíníme existenci parametru "ImageStoreConnectionString", aniž bychom popsali, co to vlastně znamená. A poté, co prochází článek, jako [je nasazení a odebrání aplikací pomocí Prostředí PowerShell][10], vypadá to, že vše, co udělat, je zkopírovat / vložit hodnotu, jak je znázorněno v manifestu clusteru cílového clusteru. Takže nastavení musí být konfigurovatelné pro každý cluster, ale při vytváření clusteru prostřednictvím [portálu Azure][11], neexistuje žádná možnost konfigurovat toto nastavení a je vždy "fabric:ImageStore". Jaký je tedy účel tohoto nastavení?

![Manifest clusteru][img_cm]

Service Fabric začal jako platforma pro interní spotřebu Microsoftu mnoha různými týmy, takže některé aspekty jsou vysoce přizpůsobitelné - "Image Store" je jedním z takových aspektů. Úložiště bitových obrázků je v podstatě připojitelné úložiště pro ukládání balíčků aplikací. Když je vaše aplikace nasazena do uzlu v clusteru, tento uzel stáhne obsah balíčku aplikace z úložiště bitových obrázků. ImageStoreConnectionString je nastavení, které obsahuje všechny potřebné informace pro klienty i uzly najít správné úložiště obrázků pro daný cluster.

V současné době existují tři možné druhy zprostředkovatelů úložiště obrázků a jejich odpovídající připojovací řetězce jsou následující:

1. Služba úložiště obrázků: "fabric:ImageStore"

2. Systém souborů: "soubor:[cesta k systému souborů]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https; AccountName=[...]; AccountKey=[...]; Kontejner=[...]"

Typ zprostředkovatele používaný v produkčním prostředí je služba Úložiště obrázků, což je stavová trvalá systémová služba, kterou můžete vidět z aplikace Service Fabric Explorer. 

![Služba úložiště obrázků][img_is]

Hostování úložiště bitových obrázků v systémové službě v rámci samotného clusteru eliminuje externí závislosti pro úložiště balíčků a dává nám větší kontrolu nad lokalitou úložiště. Budoucí vylepšení kolem úložiště obrázků pravděpodobně nejprve zacílí na poskytovatele úložiště obrázků, ne-li výhradně. Připojovací řetězec pro poskytovatele služby úložiště bitových údajů nemá žádné jedinečné informace, protože klient je již připojen k cílovému clusteru. Klient potřebuje pouze vědět, že by měly být použity protokoly zaměřené na systémovou službu.

Poskytovatel systému souborů se používá místo služby Úložiště obrázků pro místní clustery s jedním boxem během vývoje k zavádění clusteru o něco rychleji. Rozdíl je obvykle malý, ale je to užitečná optimalizace pro většinu lidí během vývoje. Je možné nasadit místní cluster s jedním panelem s jinými typy zprostředkovatele úložiště, ale obvykle k tomu není žádný důvod, protože pracovní postup vývoje a testování zůstává stejný bez ohledu na zprostředkovatele. Zprostředkovatel úložiště Azure existuje pouze pro starší podporu starých clusterů nasazených před zavedením poskytovatele služby Úložiště obrázků.

Kromě toho není zprostředkovatel souborového systému nebo zprostředkovatele úložiště Azure by měl být používán jako metoda sdílení úložiště bitových obrázků mezi více clustery – to bude mít za následek poškození konfiguračních dat clusteru jako každý cluster může zapisovat konfliktní data do bitové kopie Úložiště. Chcete-li sdílet zřízené balíčky aplikací mezi více clustery, použijte místo toho soubory [sfpkg,][12] které lze odeslat do libovolného externího úložiště s identifikátorem URI pro stahování.

Takže zatímco ImageStoreConnectionString je konfigurovatelný, stačí použít výchozí nastavení. Při publikování do Azure prostřednictvím Visual Studia se parametr automaticky nastaví podle toho. Pro programové nasazení do clusterů hostovaných v Azure je připojovací řetězec vždy "fabric:ImageStore". I když v případě pochybností, jeho hodnota může být vždy ověřena načtením manifestu clusteru [pomocí PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), nebo [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Místní testovací i produkční clustery by měly být vždy nakonfigurovány tak, aby také používaly také poskytovatele služby Úložiště obrázků.

### <a name="next-steps"></a>Další kroky
[Nasazení a odebrání aplikací pomocí PowerShellu][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
