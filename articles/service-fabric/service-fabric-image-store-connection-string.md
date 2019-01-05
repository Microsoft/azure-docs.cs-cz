---
title: Azure Service Fabric image store připojovacího řetězce | Dokumentace Microsoftu
description: Vysvětlení připojovací řetězec úložiště obrázků
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 5f69de9ba9a3226209a339c6f6fa778d9ded9755
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050980"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Principy nastavení ImageStoreConnectionString

V některých část naší dokumentace jsme stručně zmiňovat existenci parametr "ImageStoreConnectionString" bez popisující, co to vlastně znamená. A poté, co projde článku jako [nasazení a odeberte aplikací pomocí prostředí PowerShell][10], vypadá podobně jako všechny provedete, kopírování a vkládání je hodnota, jak je znázorněno v manifestu clusteru cílový cluster. Proto musí být nastavení konfigurovat na cluster, ale při vytváření clusteru prostřednictvím [webu Azure portal][11], není možnost nakonfigurovat toto nastavení a je vždy "prostředků infrastruktury: ImageStore". K čemu je toto nastavení pak?

![Manifestu clusteru][img_cm]

Service Fabric spustil(a) jako platforma pro interní použití Microsoft mnoho různých týmů, takže některé aspekty jsou vysoce přizpůsobitelné – "Image Store" je jeden takový aspekt. Image Store je v podstatě připojitelné úložiště pro ukládání balíčků aplikací. Při nasazení vaší aplikace na uzlu v clusteru, tento uzel umožňuje stažení obsahu balíčku aplikace z Image Store. ImageStoreConnectionString je nastavení, která obsahuje všechny potřebné informace pro klienty a uzly najít správnou Image Store pro daný cluster.

Aktuálně existují tři možné typy poskytovatelů Image Store a jejich odpovídající připojovací řetězce jsou následující:

1. Služba Image Store: "prostředků infrastruktury: ImageStore"

2. Systém souborů: "file:[file systémové cesty]"

3. Azure Storage: "xstore = https; AccountName = [...]; AccountKey = [...]; Kontejner = [...] "

Typ zprostředkovatele použít v produkčním prostředí je Image Store Service, které stavové trvalé systémová služba, která se zobrazí v Service Fabric Exploreru. 

![Image Store Service][img_is]

Hostování Image Store v systému služby v rámci samotného clusteru eliminuje vnější závislosti pro úložiště balíčků a získáváme větší kontrolu nad umístění úložiště. Budoucí vylepšení kolem Image Store se pravděpodobně cílit Image Store poskytovatele nejprve, pokud není výhradně. Připojovací řetězec pro poskytovatele služeb Image Store nemá žádné jedinečné informace, protože klient je již připojen do cílového clusteru. Klient stačí vědět, že by měla sloužit protokoly, které cílí na systém služby.

Zprostředkovatele systému souborů se používá namísto Image Store Service pro místní clustery jeden pole během vývoje ke spuštění clusteru mírně rychlejší. Rozdíl je obvykle malý, ale je užitečné optimalizace pro většinu lidí během vývoje. Je možné nasadit místního clusteru jeden pole s ostatních úložiště zprostředkovatele typů a, ale obvykle neexistuje žádný důvod k tomu, protože pracovní postup vývoje/testování zůstává stejný bez ohledu na poskytovatele. Zprostředkovatel služby Azure Storage k dispozici pouze pro stále podporuje starší verze starých předtím, než byla zavedena poskytovatele Image Store k nasazení clusterů.

Kromě toho není zprostředkovatele systému souborů nebo zprostředkovatele služby Azure Storage, by měla sloužit jako způsob sdílení Image Store mezi clustery s několika – bude výsledkem poškození dat konfigurace clusteru můžete každý cluster psaní konfliktní data do bitové kopie Store. Chcete-li sdílet balíčky zřízené aplikací mezi více clusterů, použijte [sfpkg] [ 12] soubory místo toho, který může být odeslán do jakéhokoli externího úložiště při stahování identifikátor URI.

Proto ImageStoreConnectionString je konfigurovat, stačí použít výchozí nastavení. Při publikování do Azure pomocí sady Visual Studio, parametr je automaticky nastaven pro vás odpovídajícím způsobem. Programové nasazení do clusterů, které jsou hostované v Azure připojovací řetězec je vždy "prostředků infrastruktury: ImageStore". I když v případě pochybností si je jeho hodnota vždy ověřit načtením manifestu clusteru podle [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), nebo [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). V místním testování a produkční clustery vždy by měl být povolen použít poskytovateli Image Store Service.

### <a name="next-steps"></a>Další postup
[Nasazení a odebírat aplikace pomocí Powershellu][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
