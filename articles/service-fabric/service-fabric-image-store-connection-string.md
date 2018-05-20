---
title: Azure řetězci úložiště bitové kopie Service Fabric | Microsoft Docs
description: Pochopení připojovací řetězec úložiště bitové kopie
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
ms.openlocfilehash: 7d164fea62afac83c4fe2216c56a9980d9279f3a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Pochopení parametr ImageStoreConnectionString nastavení

V některé dokumentaci jsme stručně zmínili existenci parametru "Parametr ImageStoreConnectionString" bez popisující, co opravdu znamená. A poté, co projde článek jako [nasazení a odeberte aplikací pomocí prostředí PowerShell][10], vypadá všechny uděláte je zkopírujte a vložte hodnotu, jak se objevuje v manifestu clusteru cílového clusteru. Takže nastavení musí být konfigurovat každý cluster, ale při vytváření clusteru prostřednictvím [portál Azure][11], není žádná možnost pro konfiguraci tohoto nastavení a je vždy "fabric: úložiště bitových kopií". Jaký je účel toto nastavení pak?

![Manifestu clusteru][img_cm]

Service Fabric spuštěna jako platformu pro interní používání Microsoft mnoha různými týmy, takže některé aspekty jsou vysoce přizpůsobitelné – "Image Store" je jeden takový aspekt. Úložiště bitových kopií je v podstatě modulární úložiště pro ukládání balíčky aplikací. Pokud vaše aplikace je nasazená do uzlu v clusteru, tento uzel stáhne obsah balíčku aplikace z úložiště bitových kopií. Parametr ImageStoreConnectionString je nastavení, která zahrnuje všechny potřebné informace pro klienty a uzly najít správný úložiště bitových kopií pro daný cluster.

Aktuálně existují tři možné typy poskytovatelů úložiště Image Store a jejich odpovídající připojovací řetězce jsou následující:

1. Služba úložiště Image Store: "fabric: úložiště bitových kopií"

2. Systém souborů: "file:[file systému cesta]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Kontejner = [...] "

Typ zprostředkovatele použít v produkčním prostředí je službu Store bitové kopie, což je stavová trvalou systémová služba, která se zobrazí v Service Fabric Exploreru. 

![Služby úložiště bitových kopií][img_is]

Hostování v systému služby v rámci samotného clusteru je úložiště Image Store eliminuje vnější závislosti pro úložiště balíčků a nám dává větší kontrolu nad polohu úložiště. Budoucí vylepšení kolem je úložiště Image Store se pravděpodobně cíle zprostředkovatele úložiště Image Store nejprve, není-li výhradně. Připojovací řetězec pro poskytovatele služeb úložiště bitové kopie nemá žádné jedinečné informace, protože klient je již připojen do cílového clusteru. Klient pouze musí vědět, že má být použita protokoly cílení na službu system.

Zprostředkovatele systému souborů se používá místo službu úložiště bitové kopie pro místní clustery jeden pole během vývoje k navázání připojení clusteru mírně rychlejší. Rozdíl je obvykle malý, ale je užitečné optimalizace pro většinu zaměstnance během vývoje. Je možné nasadit místní cluster jeden pole s ostatních úložiště zprostředkovatele typů a, ale obvykle neexistuje žádný důvod k tomu, protože pracovního postupu pro vývoj/testování zůstává stejný bez ohledu na zprostředkovatele. Zprostředkovatel úložiště Azure existuje pouze pro podporuje starší verze starého clustery nasazené, než byla zavedena poskytovatele služeb úložiště bitové kopie.

Kromě toho ani zprostředkovatele systému souborů ani poskytovatele úložiště Azure má být použit jako metodu sdílení úložišti bitové kopie mezi clustery s několika – tato akce způsobí poškození dat konfigurace clusteru, protože každý cluster můžete zapsat konfliktní data do Úložiště bitových kopií. Chcete-li sdílet balíčky zřízené aplikací mezi více clusterů, použijte [sfpkg] [ 12] soubory místo toho, který je možné uložit do jakékoli externí úložiště ke stahování identifikátor URI.

Proto při konfigurovat parametr ImageStoreConnectionString obecně právě používáte výchozí nastavení. Při publikování do Azure pomocí sady Visual Studio, parametr je automaticky nastaven pro vás odpovídajícím způsobem. Programová nasazení do clusterů, které jsou hostované v Azure připojovací řetězec je vždy "fabric: úložiště bitových kopií". V případě, že pokud máte pochybnosti, jeho hodnota vždy ověřovány načítání manifestu clusteru podle [prostředí PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), nebo [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Místní testování a produkčních clusterů by měl být vždy nakonfigurovaný na použití i poskytovatele služby úložiště bitové kopie.

### <a name="next-steps"></a>Další postup
[Nasazení a odebírat aplikace pomocí prostředí PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
