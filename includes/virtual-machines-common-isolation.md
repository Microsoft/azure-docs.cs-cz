---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123244"
---
Azure Compute nabízí velikosti virtuálních počítačů, které jsou izolované pro konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.  Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysoký stupeň izolace od ostatních zákazníků pro úlohy zahrnující prvky, jako je dodržování předpisů a regulační požadavky.  Zákazníci se taky můžou rozhodnout dále rozdělit prostředky těchto izolovaných virtuálních počítačů pomocí [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Využití izolované velikosti zaručuje, že váš virtuální počítač bude jediný spuštěný na této konkrétní instanci serveru.  Aktuální nabídky izolovaného virtuálního počítače zahrnují:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Další informace o jednotlivých dostupných izolovaných velikostech naleznete [zde](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Odchod do důchodu D15_v2/DS15_v2 izolace v květnu 15, 2020
**Aktualizace na únor 10, 2020: "Izolace" odchod do důchodu časová osa byla prodloužena do května 15, 2020"**

Azure Dedicated Host je teď GA, který umožňuje spouštět virtuální počítače linuxa a Windows vaší organizace na fyzických serverech s jedním tenantem. Plánujeme plně nahradit izolované virtuální počítače Azure pomocí vyhrazeného hostitele Azure. **května 2020** D15_v2/DS15_v2 virtuální počítače Azure už nebude izolovaný hardware.

## <a name="how-does-this-affect-me"></a>Co to pro mě znamená?
května 2020 už nebudeme poskytovat záruku izolace pro vaše virtuální počítače D15_v2/DS15_v2 Azure. 

## <a name="what-actions-should-i-take"></a>Jaké kroky mám podniknout?
Pokud pro vás není vyžadována izolace hardwaru, není třeba provést žádnou akci. 

Pokud je pro vás vyžadována izolace, před 15.

• [Migrujte](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) své úlohy do vyhrazeného hostitele Azure.

• [Požádejte o přístup](https://aka.ms/D15iRequestAccess) k D15i_v2 a DS15i_v2 virtuálním počítači Azure, abyste získali stejný výkon. Tato možnost je k dispozici pouze pro scénáře průběžných plateb a jeden rok rezervované instance.    

• [Migrujte](https://azure.microsoft.com/blog/resize-virtual-machines/) úlohu do jiného izolovaného virtuálního počítače Azure. 

Podrobnosti viz níže:

## <a name="timeline"></a>Časová osa
| Datum | Akce | 
| --- | --- |
| 18. listopadu 2019 | Dostupnost D/DS15i_v2 (PAYG, 1-rok RI) |
| 14. května 2020  | Poslední den koupit D / DS15i_v2 1-rok RI | 
| 15. května 2020   | Odstraněna záruka izolace D/DS15_v2 | 
| 15. května 2021  | Odejít do důchodu D/DS15i_v2 (všichni zákazníci kromě těch, kteří si koupili 3letou RI D/DS15_v2 před listopadem 18, 2019)| 
| 17. listopadu 2022November 17, 2022  | Odejít do důchodu D/ DS15i_v2 po dokončení tříletých RI (pro zákazníky, kteří si koupili 3letou RI D / DS15_v2 před listopadem 18, 2019) | 

## <a name="faq"></a>Nejčastější dotazy
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>Otázka: Je velikost D / DS15_v2 dostane do důchodu?
**A:** Ne, pouze "izolace" funkce se chystá dostat do důchodu. Pokud nepotřebujete izolaci, nemusíte provádět žádnou akci.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>Otázka: Je velikost D / DS15i_v2 dostane v důchodu?
**A:** Ano, velikost je k dispozici pouze do května 15,2021. Pro zákazníky, kteří si zakoupili 3-leté RIs na D / DS15_v2 před listopadem 18, 2019 bude mít přístup k D / DS15i_v2 až do listopadu 17, 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>Otázka: Proč se na portálu nezobrazují nové velikosti D/DS15i_v2?
**A**: Pokud jste aktuální D/ DS15_v2 zákazníka a chcete použít nové Velikosti D/DS15i_v2, vyplňte tento [formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>Otázka: Proč se mi nezobrazuje žádná kvóta pro nové velikosti D/DS15i_v2?
**A**: Pokud jste aktuální D/ DS15_v2 zákazníka a chcete použít nové Velikosti D/DS15i_v2, vyplňte tento [formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Otázka: Kdy budou ostatní izolované velikosti vyřazeny do důchodu?
**A**: Budeme poskytovat připomenutí 12 měsíců před oficiálním vyřazením z provozu velikostí.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Otázka: Existuje prostoje, když můj virtuální počítač přistane na neizolovaném hardwaru?
**A**: Pokud nepotřebujete izolaci, nemusíte provádět žádnou akci a neuvidíte žádné prostoje.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>Otázka: Existují nějaké změny nákladů pro přechod na neizolovaný virtuální počítač?
**A**: Ne 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>Otázka: Již jsem zakoupil 1 nebo 3letou rezervovanou instanci pro D15_v2 nebo Ds15_v2. Jak se bude sleva použít na využití virtuálního počítače?
**A**: Ru zakoupené před listopadem 18, 2019 automaticky rozšíří pokrytí na novou izolovanou řadu virtuálních zařízení. 

| RI |  Flexibilita velikosti instance | Nárok na zaměstnanecké výhody |   
| --- | --- | --- |
|   D15_v2  |   Vypnuto     |   D15_v2 a D15i_v2 |    
|   D15_v2  |   Zapnuto  |   D15_v2 série a D15i_v2 obdrží všechny výhody RI. |    
|   D14_v2  |   Zapnuto  |   D15_v2 série a D15i_v2 obdrží všechny výhody RI. |    
 
Stejně tak pro řadu Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>Otázka: Chci zakoupit další rezervované instance pro Dv2. Který z nich si mám vybrat?
**A**: Všechny RU zakoupené po 18 listopadu 2019 mají následující chování. 

| RI |  Flexibilita velikosti instance | Nárok na zaměstnanecké výhody |   
| --- | --- | --- |
| D15_v2 |  Vypnuto |   pouze D15_v2  
| D15_v2 |  Zapnuto |    D15_v2 série obdrží výhodu RI. Nové D15i_v2 nebudou způsobilé pro prospěch RI z tohoto typu RI. | 
| D15i_v2 |     Vypnuto | pouze D15i_v2 |  
| D15i_v2 |     Zapnuto  | pouze D15i_v2 | 
 
Flexibilitu velikosti instance nelze použít k použití pro jiné velikosti, jako je D2_v2, D4_v2 nebo D15_v2. Stejně tak pro řadu Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>Otázka: Mohu si koupit nové tříleté RI pro D15i_v2 a DS15i_v2?
**A:** Bohužel ne, pouze 1-rok RI je k dispozici pro nový nákup.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>Otázka: Lze přesunout stávající instanci rezervy D15_v2/DS15_v2 na rezervovanou instanci izolované velikosti?
**A**: Tato akce není nutná, protože výhoda se bude vztahovat na izolované i neizolované velikosti. Azure ale bude podporovat změnu stávajících D15_v2/DS15_v2 rezervovaných instancí na D15i_v2/DS15i_v2. Pro všechny ostatní rezervované instance Dv2/Dsv2 použijte existující rezervovanou instanci nebo koupit nové rezervované instance pro izolované velikosti.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Otázka: Jsem zákazník azure service fabric, který spoléhá na úrovně odolnosti silver nebo gold. Má tato změna vliv na mě?
**A:** Ne. Záruky poskytované [úrovněmi odolnosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) service fabric budou fungovat i po této změně. Pokud požadujete izolaci fyzického hardwaru z jiných důvodů, může být stále nutné provést jednu z výše popsaných akcí. 
