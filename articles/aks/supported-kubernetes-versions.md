---
title: Podporované verze Kubernetes ve službě Azure Kubernetes Service
description: Principy zásad podpory verzí Kubernetes a životního cyklu clusterů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593440"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunita Kubernetes vydává menší verze zhruba každé tři měsíce. Tyto verze obsahují nové funkce a vylepšení. Vydání oprav jsou častější (někdy i týdně) a jsou určena pouze pro opravy kritických chyb v dílčí verzi. Tyto verze oprav zahrnují opravy chyb zabezpečení nebo hlavních chyb, které mají dopad na velký počet zákazníků a produktů spuštěných v produkčním prostředí na základě Kubernetes.

AKS si klade za cíl certifikovat a vydat nové verze Kubernetes do 30 dnů od vydání upstream, s výhradou stability vydání.

## <a name="kubernetes-versions"></a>Verze Kubernetes

Kubernetes používá standardní schéma [samantického správu verzí.](https://semver.org/) To znamená, že každá verze Kubernetes se řídí tímto schématem číslování:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Každé číslo ve verzi označuje obecnou kompatibilitu s předchozí verzí:

* Hlavní verze se změní, když mohou být přerušeny nekompatibilní změny rozhraní API nebo zpětná kompatibilita.
* Dílčí verze se změní při provádění změn funkčnosti, které jsou zpětně kompatibilní s ostatními dílčími verzemi.
* Verze oprav se mění při provedení zpětně kompatibilních oprav chyb.

Uživatelé by se měli snažit spustit nejnovější verzi opravy dílčí verze, kterou používají, například pokud je váš produkční cluster na *1.12.14* a *1.12.15* je nejnovější dostupná verze opravy dostupná pro řadu *1.12,* měli byste upgradovat na *verzi 1.12.15,* jakmile budete moci zajistit, že váš cluster je plně opraven a podporován.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verze Kubernetes

AKS podporuje tři dílčí verze Kubernetes:

* Aktuální dílčí verze, která je vydána v AKS (N)
* Dvě předchozí dílčí verze. Každá podporovaná dílčí verze také podporuje dvě stabilní opravy.

To to je známé jako "N-2": (N (Nejnovější verze) - 2 (dílčí verze)).

Například pokud AKS zavádí *1.15.a* dnes, podpora je poskytována pro následující verze:

Nová dílčí verze    |    Seznam podporovaných verzí
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

Kde ".letter" je reprezentativní verze patch.

Podrobnosti o komunikaci týkající se změn verzí a očekávání naleznete níže v části Komunikace.

Když je zavedena nová dílčí verze, nejstarší dílčí verze a aktualizace opravy podporované jsou zastaralé a odebrány. Pokud je například aktuální seznam podporovaných verzí následující:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

A AKS vydává 1.16. *, to znamená, že 1,13.* verze (všechny verze 1.13) budou odebrány a jsou mimo podporu.

> [!NOTE]
> Upozorňujeme, že pokud zákazníci používají nepodporovanou verzi Kubernetes, budou požádáni o upgrade při žádosti o podporu pro cluster. Clustery s nepodporovanými verzemi Kubernetes nejsou zahrnuty v [zásadách podpory AKS](https://docs.microsoft.com/azure/aks/support-policies).

Kromě výše uvedeného na dílčích verzích, AKS podporuje dvě nejnovější **verze patch** dané dílčí verze. Například vzhledem k následující podporované verze:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Pokud upstream Kubernetes vydala 1.15.3 a 1.14.6 a AKS vydává tyto verze oprav, nejstarší verze oprav jsou zastaralé a odebrány a podporovaný seznam verzí se stane:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Komunikace

* Pro nové **dílčí** verze Kubernetes
  * Všichni uživatelé jsou veřejně upozorněni na novou verzi a jaká verze bude odebrána.
  * Po vydání nové verze opravy je současně odebrána nejstarší verze opravy.
  * Zákazníci mají **30 dní** od data oznámení veřejného oznámení na upgrade na podporovanou verzi dílčí verze.
* Pro nové **patch** verze Kubernetes
  * Všichni uživatelé jsou upozorněni na vydání nové verze opravy a upgrade na nejnovější verzi opravy.
  * Uživatelé mají **30 dní** na upgrade na novější podporovanou verzi opravy před odebráním nejstarší.

AKS definuje "vydanou verzi" jako obecně dostupné verze, povolené ve všech měřeních SLO / Quality of Service a dostupné ve všech oblastech. AKS může také podporovat verze Preview, které jsou explicitně označeny a podléhají podmínkám náhledu.

#### <a name="notification-channels-for-aks-changes"></a>Kanály oznámení pro změny AKS

AKS publikuje pravidelné aktualizace služeb, které shrnují nové verze Kubernetes, změny služeb a aktualizace komponent, které byly vydány ve službě na [GitHubu](https://github.com/Azure/AKS/releases).

Tyto změny jsou vráceny všem zákazníkům jako součást pravidelné údržby, která je nabízena jako součást spravované služby, některé vyžadují explicitní upgrady, zatímco jiné nevyžadují žádnou akci.

Oznámení jsou také zasílána prostřednictvím:

* [Poznámky k verzi AKS](https://aka.ms/aks/releasenotes)
* Oznámení portálu Azure Portal
* [Kanál aktualizace Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Výjimky zásad podporovaných verzí

AKS si vyhrazuje právo přidat nebo odebrat nové/existující verze, u kterých bylo zjištěno, že mají jednu nebo více kritických produkčních chyb nebo bezpečnostních problémů bez předchozího upozornění.

Konkrétní verze oprav mohou být přeskočeny nebo zrychlené zavedení v závislosti na závažnosti chyby nebo problému se zabezpečením.

### <a name="azure-portal-and-cli-default-versions"></a>Výchozí verze portálu Azure a cli

Když nasadíte cluster AKS na portálu nebo pomocí azure CLI, cluster je výchozí verze N-1 dílčí a nejnovější oprava. Pokud například AKS podporuje *1.15.a*, *1.15.b*, *1.14.c*, *1.14.d*, *1.13.e*a *1.13.f*, je výchozí verze vybrána *1.14.c*.

AKS zvolí výchozí N-1 poskytnout zákazníkům známé, stabilní a oprava verze ve výchozím nastavení.

## <a name="list-currently-supported-versions"></a>Seznam aktuálně podporovaných verzí

Chcete-li zjistit, jaké verze jsou aktuálně k dispozici pro vaše předplatné a oblast, použijte příkaz [az aks get-versions.][az-aks-get-versions] V následujícím příkladu jsou uvedeny dostupné verze Kubernetes pro oblast *EastUS:*

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Nejčastější dotazy

**Co se stane, když zákazník upgraduje cluster Kubernetes s dílčí verzí, která není podporována?**

Pokud jste na *n-3* verzi, jste mimo podporu a bude vyzván k upgradu. Pokud je váš upgrade z verze n-3 na n-2 úspěšný, jste nyní v rámci našich zásad podpory. Například:

- Pokud nejstarší podporovaná verze AKS je *1.13.a* a jste na *1.12.b* nebo starší, jste mimo podporu.
- Pokud bude upgrade z *1.12.b* na *1.13.a* nebo vyšší úspěšný, jste zpět v rámci našich zásad podpory.

Upgrady na verze starší než podporované okno *N-2* nejsou podporovány. V takových případech doporučujeme zákazníkům vytvořit nové clustery AKS a znovu nasadit své úlohy s verzemi v podporovaném okně.

**Co znamená "Mimo podporu"**

"Mimo podporu" znamená, že verze, kterou používáte, je mimo seznam podporovaných verzí a budete při žádosti o podporu vyzváni k upgradu clusteru na podporovanou verzi. Navíc AKS neprovádí žádné runtime nebo jiné záruky pro clustery mimo seznam podporovaných verzí.

**Co se stane, když zákazník škáluje cluster Kubernetes s dílčí verzí, která není podporována?**

U dílčích verzí, které AKS nepodporuje, by škálování nebo vyzdále fungovat, ale důrazně doporučujeme upgradovat, aby se váš cluster vrátil zpět do podpory.

**Může zákazník zůstat na verzi Kubernetes navždy?**

Ano. Pokud však cluster není v jedné z verzí podporovaných Službou AKS, je cluster mimo zásady podpory AKS. Azure automaticky neupgraduje váš cluster ani ho neodstraňuje.

**Jakou verzi podporuje řídicí rovina, pokud fond uzlů není v jedné z podporovaných verzí AKS?**

Rovina ovládacího prvku musí být v okně verzí ze všech fondů uzlů. Podrobnosti o inovaci fondů řídicí roviny nebo uzlů naleznete v dokumentaci k [upgradu fondů uzlů](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak upgradovat cluster, najdete [v tématu Upgrade clusteru služby Azure Kubernetes Service (AKS).][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
