---
title: Informace o funkci Mobile Apps v Azure App Service
description: Zjistěte, jaké výhody App Service přináší pro vaše firemní mobilní aplikace.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5df59e886a2a4950e2ca6e1ef96487d3d59e7524
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718125"
---
# <a name="getting-started"></a>Informace o funkci Mobile Apps v Azure App Service
Azure App Service nabízí plně spravovanou [platformu jako službu](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) pro profesionální vývojáře. Tato služba přináší bohatou sadu možností pro webové, mobilní a integrační scénáře. 

Funkce Mobile Apps služby Azure App Service nabízí podnikovým vývojářům a systémovým integrátorům vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací.

![Vizuální přehled možností funkce Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Proč Mobile Apps?
S funkcí Mobile Apps můžete:

* **Vytvářejte nativní a multiplatformní aplikace**: Ať už vytváříte nativní aplikace pro iOS, Android a Windows apps nebo Xamarin nebo Cordova (PhoneGap) napříč platformami aplikací, můžete využít služby App Service pomocí sady SDK pro nativní.
* **Připojení k podnikovým systémům**: S funkcí Mobile Apps můžete přidání podnikového přihlašování v řádu minut a a připojte se k vaší organizace v místním nebo cloudovým prostředkům.
* **Vytvářet aplikace připravené pro režim offline synchronizací dat**: Zvýší produktivitu mobilních pracovníků zajistíte vytvářením aplikací, které fungují offline a využívají Mobile Apps k synchronizaci dat na pozadí, když se obnoví připojení se zdroji firemních dat nebo software jako služba (SaaS) rozhraní API.
* **Nabízená oznámení milionům příjemců v řádu sekund**: Zákazníky oslovíte pomocí rychlých nabízených oznámení pro všechna zařízení, přizpůsobovat podle jejich potřeb a zasílat správný čas.

## <a name="mobile-apps-features"></a>Funkce Mobile Apps
Pro vývoj mobilních řešení využívajících cloud jsou důležité následující funkce:

* **Ověřování a autorizace**: Podpora zprostředkovatelů identity, včetně Azure Active Directory pro podnikové ověřování i poskytovatelé služeb sociálních sítí, jako je Facebook, Google, Twitter a Microsoft účty. Funkce Mobile Apps nabízí službu OAuth 2.0 pro každého zprostředkovatele. Můžete také integrovat sadu SDK pro daného zprostředkovatele, aby se zajistila specifická funkcionalita.

    Projděte si další informace o [funkcích ověřování].

* **Přístup k datům**: Mobile Apps poskytuje zdroj dat mobilní zařízení OData v3, který je propojený s Azure SQL Database nebo SQL server v místním. Protože tato služba může být založena na rozhraní Entity Framework, můžete ji snadno integrovat s jinými poskytovateli dat NoSQL i SQL, včetně [Azure Table Storage], MongoDB, [Azure Cosmos DB] a poskytovateli rozhraní API pro SaaS, jako je Office 365 a Salesforce.com.

* **Offline synchronizace**: Klient sady SDK usnadňují vytváření robustních a rychle reagující mobilních aplikací, které pracují s offline datovou sadou. Tuto datovou sadu můžete automaticky synchronizovat s daty back-end, včetně podpory řešení konfliktů.

  Projděte si další informace o [datových funkcích].

* **Nabízená oznámení**: Klientské sady SDK se hladce integrují s registračními funkcemi služby Azure Notification Hubs, takže je můžete současně zasílat nabízená oznámení milionům uživatelů.

  Projděte si další informace o [funkcích pro nabízená oznámení].

* **Klientské sady SDK**: Je kompletní paletu klientských sad SDK, které zahrnují nativní vývoj ([iOS], [Android], a [Windows]), multiplatformní vývoj ([Xamarin.iOS a Xamarin.Android], [Xamarin.Forms]) a vývoj hybridních aplikací ([Apache Cordova]). Všechny klientské sady SDK jsou k dispozici s licencí MIT a jako open-source.

## <a name="azure-app-service-features"></a>Funkce Azure App Service
U systémů pro mobilní zařízení v provozním prostředí jsou užitečné následující funkce platformy:

* **Automatické škálování**: Pomocí služby App Service můžete rychle vertikálně navýšit kapacitu nebo horizontální navýšení kapacity pro zpracování jakéhokoli příchozího vytížení od zákazníka. Počet a velikost virtuálních počítačů můžete vybrat ručně nebo je možné u back-endu mobilní aplikace nastavit automatické škálování podle plánu nebo momentální zátěže.

  Projděte si další informace o [automatickém škálování].

* **Pracovní prostředí**: App Service umožňuje spuštění několika verzí webu, takže můžete provádět A / B testování, testování v produkčním prostředí v rámci většího plánu DevOps a v místně fázovat nový back-end.

  Projděte si další informace o [přípravná prostředí].

* **Průběžné nasazování**: App Service umožňuje integraci s běžnými _správy zdrojového kódu_ systémy (SCM), umožňuje snadno nasadit novou verzi back-endu.

  Projděte si další informace o [možnostech nasazení](../app-service/deploy-local-git.md).

* **Virtuální síť**: App Service umožňuje připojení k místním prostředkům pomocí virtuální sítě, Azure ExpressRoute nebo hybridní připojení.

  Projděte si další informace pro [hybridní připojení], [virtuální sítě] a [ExpressRoute].

* **Izolovaná a vyhrazená prostředí**: Pro bezpečné spouštění aplikací Azure App Service, můžete spustit v plně izolovaném a vyhrazeném prostředí služby App Service. Toto prostředí je ideální pro úlohy aplikací vyžadující velké škálování, izolaci nebo zabezpečený přístup k síti.

  Projděte si další informace o [Prostředí App Service].

## <a name="next-steps"></a>Další postup

Pokud chcete začít s funkcí Mobile Apps ve službě Azure App Service, dokončete [úvodní] kurz. Tento kurz popisuje základy vytváření libovolného mobilního back-endu a klienta. Zabývá se také integrací ověřování, offline synchronizací a nabízenými oznámeními. Kurz můžete absolvovat vícekrát, pokaždé pro jinou klientskou aplikaci.

Pokud chcete další informace o funkci Mobile Apps, prohlédněte si naši [mapu kurzů].
Další informace o platformě Azure App Service najdete v článku [Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[úvodní]: app-service-mobile-ios-get-started.md
[Azure Table storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[funkcích ověřování]: ./app-service-mobile-auth.md
[datových funkcích]: ./app-service-mobile-offline-data-sync.md
[funkcích pro nabízená oznámení]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS a Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatickém škálování]: ../app-service/web-sites-scale.md
[přípravná prostředí]: ../app-service/deploy-staging-slots.md
[hybridní připojení]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuální sítě]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[Prostředí App Service]: ../app-service/environment/intro.md
[mapu kurzů]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
