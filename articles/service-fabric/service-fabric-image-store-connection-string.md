---
title: Připojovací řetězec pro úložiště imagí v Azure Service Fabric
description: Seznamte se s připojovacím řetězcem úložiště imagí, včetně jeho použití a aplikací do clusteru Service Fabric.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 8fc0239dd18fc7071823a129a7dbc4f102023d66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86246192"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Principy nastavení ImageStoreConnectionString

V některé z našich dokumentů stručně uvádíme existenci parametru "ImageStoreConnectionString" bez popisu toho, co skutečně znamená. A po Projděte si článek, jako je třeba [nasazení a odebrání aplikací pomocí PowerShellu][10], vypadá to, že to znamená, že se zkopíruje nebo vloží hodnota, jak je znázorněno v manifestu clusteru cílového clusteru. Nastavení se proto musí konfigurovat na cluster, ale když vytvoříte cluster prostřednictvím [Azure Portal][11], neexistuje možnost konfigurovat toto nastavení a je to vždycky "Fabric: úložiště bitových kopií". Jaký je účel tohoto nastavení?

![Manifest clusteru][img_cm]

Service Fabric se vypnula jako platforma pro interní spotřebu Microsoftu pomocí mnoha různých týmů, takže některé jeho aspekty jsou vysoce přizpůsobitelné – "Image Store" je takovým aspektem. V podstatě je Image Store připojitelné úložiště pro ukládání balíčků aplikací. Když je vaše aplikace nasazená na uzel v clusteru, tento uzel stáhne obsah balíčku aplikace z Image Store. ImageStoreConnectionString je nastavení, které zahrnuje všechny nezbytné informace pro klienty i uzly, aby bylo možné najít správné Image Store pro daný cluster.

Aktuálně existují tři možné typy zprostředkovatelů Image Store a jejich odpovídající připojovací řetězce jsou následující:

1. Služba Image Store: Fabric: úložiště bitových kopií

2. Systém souborů: soubor: [cesta systému souborů]

3. Azure Storage: "xstore: DefaultEndpointsProtocol = https; Account = [...]; AccountKey = [...]; Container = [...] "

Typ poskytovatele použitý v produkčním prostředí je služba Image Store, což je stavová trvalá systémová služba, kterou vidíte v Service Fabric Explorer. 

![Služba Image Store][img_is]

Hostování Image Store v systémové službě v rámci clusteru eliminuje externí závislosti pro úložiště balíčků a poskytuje větší kontrolu nad umístěním úložiště. Budoucí vylepšení Image Storeů se budou muset nejprve zaměřit na poskytovatele Image Store, pokud to není výhradně. Připojovací řetězec pro poskytovatele služby Image Store nemá žádné jedinečné informace, protože klient je již připojen k cílovému clusteru. Klient musí mít jenom jistotu, že by se měly používat protokoly cílené na systémovou službu.

Namísto služby Image Store pro místní clustery s jedním box se při vývoji používá poskytovatel systému souborů, aby se cluster trochu urychlil. Rozdíl je obvykle malý, ale je užitečnou optimalizací pro většinu lidé během vývoje. Je možné také nasadit místní cluster s jedním polem s jinými typy poskytovatele úložiště, ale většinou to není důvod, protože pracovní postup vývoje/testování zůstává stejný bez ohledu na poskytovatele. Poskytovatel Azure Storage existuje jenom pro podporu starší verze starých clusterů nasazených před tím, než se zavedl poskytovatel služby Image Store.

Kromě toho není třeba poskytovatele systému souborů nebo poskytovatel Azure Storage použít jako metodu sdílení Image Store mezi několika clustery – to bude mít za následek poškození dat konfigurace clusteru, protože každý cluster může do Image Store zapisovat konfliktní data. Chcete-li sdílet zřízené balíčky aplikací mezi několika clustery, použijte místo toho soubory [sfpkg][12] , které lze odeslat do libovolného externího úložiště s identifikátorem URI pro stažení.

Takže i když je ImageStoreConnectionString konfigurovatelné, stačí použít výchozí nastavení. Při publikování do Azure pomocí sady Visual Studio se parametr automaticky nastaví za vás. Pro programové nasazení do clusterů hostovaných v Azure je připojovací řetězec vždy "Fabric: úložiště bitových kopií". I když v nejistých případech může být jeho hodnota vždy ověřena načtením manifestu clusteru pomocí [PowerShellu](/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](/previous-versions/azure/reference/mt161375(v=azure.100))nebo [REST](/rest/api/servicefabric/get-a-cluster-manifest). Místní testovací i produkční clustery by měly být vždy nakonfigurované na používání poskytovatele Image Store služby.

### <a name="next-steps"></a>Další kroky
[Nasazení a odebrání aplikací pomocí PowerShellu][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
