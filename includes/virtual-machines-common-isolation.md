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
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2020
ms.locfileid: "77123244"
---
Azure COMPUTE nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.  Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysokou úroveň izolace od jiných zákazníků pro úlohy zahrnující prvky, jako jsou dodržování předpisů a zákonné požadavky.  Zákazníci si také můžou rozdělit prostředky těchto izolovaných virtuálních počítačů s využitím [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Využitím izolované velikosti zaručujete, že váš virtuální počítač bude jediným operačním systémem, který běží na konkrétní instanci serveru.  Mezi aktuální nabídky izolovaných virtuálních počítačů patří:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Další informace o všech dostupných izolovaných velikostech najdete [tady](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Vyřazení D15_v2/DS15_v2 izolace na 15. května 2020
**Aktualizace od 10. února 2020: časová osa "izolace" se rozšířila na 15. května 2020 "**

Vyhrazený hostitel Azure je teď GA, což umožňuje provozovat virtuální počítače se systémy Linux a Windows ve vaší organizaci na fyzických serverech s jedním klientem. Plánujeme plně nahradit izolované virtuální počítače Azure pomocí vyhrazeného hostitele Azure. Až **15. května 2020** D15_v2/DS15_v2 virtuální počítače Azure už nebudou izolované hardwarem.

## <a name="how-does-this-affect-me"></a>Co to pro mě znamená?
Po 15. května 2020 už nebudeme pro vaše D15_v2/DS15_v2 virtuálních počítačů Azure poskytovat záruku izolace. 

## <a name="what-actions-should-i-take"></a>Jaké akce mám provést?
Pokud se pro vás nepožaduje izolace hardwaru, není nutné provádět žádnou akci. 

Pokud se vám pro vás vyžaduje izolace, před 15. května 2020 byste museli mít jednu z těchto akcí:

• [Migrujte](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) své úlohy na vyhrazeného hostitele Azure.

• [Vyžádejte si přístup](https://aka.ms/D15iRequestAccess) k D15i_v2 a DS15i_v2 virtuálního počítače Azure, abyste získali stejný cenový výkon. Tato možnost je dostupná jenom pro scénáře s průběžnými platbami podle aktuálního využití a jednoleté rezervované instance.    

• [Migrujte](https://azure.microsoft.com/blog/resize-virtual-machines/) svoji úlohu na jiný virtuální počítač s izolací Azure. 

Podrobnosti najdete níže:

## <a name="timeline"></a>Časová osa
| Datum | Akce | 
| --- | --- |
| 18. listopadu 2019 | Dostupnost D/DS15i_v2 (PAYG, 1-year) |
| 14. května 2020  | Poslední den nákupu D/DS15i_v2 1-Year | 
| 15. května 2020   | Odebrání záruky izolace D/DS15_v2 | 
| 15. května 2021  | Vyřazení D/DS15i_v2 (všichni zákazníci s výjimkou zakoupených 3 DS15_v2 let a více než 18. listopadu 2019)| 
| 17. listopadu 2022  | Vyřazení D/DS15i_v2, když se dokončí 3 roky služby RIs (pro zákazníky, kteří koupili 3 roky rezervované instance u D/DS15_v2 do 18. listopadu 2019) | 

## <a name="faq"></a>Nejčastější dotazy
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>Otázka: je velikost D/DS15_v2, která bude vyřazení?
Odpověď **: ne**, přestanou být vyřazení pouze funkce izolace. Pokud nepotřebujete izolaci, nemusíte provádět žádnou akci.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>Otázka: je velikost D/DS15i_v2, která bude vyřazení?
Odpověď **: Ano**, velikost je k dispozici pouze do 15. května 2021. Pro zákazníky, kteří si zakoupili tři roky na D/DS15_v2 před 18. listopadu 2019, budou mít přístup k D/DS15i_v2 až do 17. listopadu 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>Otázka: Proč se mi na portálu nezobrazuje nové velikosti D/DS15i_v2?
Odpověď **: Pokud**jste aktuální zákazník D/DS15_v2 a chcete používat nové velikosti d/DS15i_v2, vyplňte tento [formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u) .

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>Otázka: Proč se mi nezobrazuje žádná kvóta pro nové velikosti D/DS15i_v2?
Odpověď **: Pokud**jste aktuální zákazník D/DS15_v2 a chcete používat nové velikosti d/DS15i_v2, vyplňte tento [formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u) .

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Otázka: kdy jsou jiné izolované velikosti vyřazení?
Odpověď: zadáte připomenutí 12 měsíců předem k oficiálnímu vyřazení velikostí z provozu.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Otázka: dojde k výpadku, když je můj virtuální počítač na neizolovaný hardware?
Odpověď **: Pokud**nepotřebujete izolaci, nemusíte provádět žádnou akci a nemůžete se podívat na výpadky.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>Otázka: jsou nějaké změny nákladů na přechod na neizolovaný virtuální počítač?
Odpověď **: ne** 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>Otázka: již jsme zakoupili rezervovanou instanci na 1 nebo 3 roky pro D15_v2 nebo Ds15_v2. Jak se použije sleva na využití virtuálních počítačů?
Odpověď **: služba se**zakoupila do 18. listopadu 2019 automaticky rozšíří pokrytí do nové izolované řady virtuálních počítačů. 

| NEMUSÍ |  Flexibilita velikosti instance | Nárok na zvýhodnění |   
| --- | --- | --- |
|   D15_v2  |   Vypnuto     |   D15_v2 a D15i_v2 |    
|   D15_v2  |   Zapnuto  |   Výhody D15_v2 řady a D15i_v2 získají výhodu rezervované instance. |    
|   D14_v2  |   Zapnuto  |   Výhody D15_v2 řady a D15i_v2 získají výhodu rezervované instance. |    
 
Podobně jako u řady Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>Otázka: Chci si koupit další rezervované instance pro Dv2. Který z nich mám zvolit?
Odpověď **: všechny**rezervované instance zakoupené po 18. listopadu 2019 mají následující chování. 

| NEMUSÍ |  Flexibilita velikosti instance | Nárok na zvýhodnění |   
| --- | --- | --- |
| D15_v2 |  Vypnuto |   Pouze D15_v2  
| D15_v2 |  Zapnuto |    Řada D15_v2 obdrží výhodu rezervované instance. Nový D15i_v2 nebude mít nárok na zvýhodnění rezervované instance z tohoto typu. | 
| D15i_v2 |     Vypnuto | Pouze D15i_v2 |  
| D15i_v2 |     Zapnuto  | Pouze D15i_v2 | 
 
Flexibilita velikosti instance se nedá použít pro použití na jiné velikosti, jako je D2_v2, D4_v2 nebo D15_v2. Podobně pro Dsv2 Series.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>Otázka: mohu koupit nové 3 roky pro D15i_v2 a DS15i_v2?
Odpověď **: Bohužel**ne, pro nové nákupy je k dispozici pouze 1 rok rezervované instance.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>Otázka: Mohu přesunout stávající instanci rezervovat D15_v2/DS15_v2 na izolovanou rezervovanou velikost?
Odpověď **: Tato**akce není nutná, protože výhoda bude platit pro izolované i neizolované velikosti. Ale Azure bude podporovat změnu stávajících D15_v2/DS15_v2 rezervovaných instancí na D15i_v2/DS15i_v2. U všech ostatních rezervovaných instancí Dv2/Dsv2 použijte existující rezervovanou instanci nebo kupte nové rezervované instance pro izolované velikosti.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Otázka: jsem zákazníkem Service Fabric Azure, který se spoléhá na úroveň odolnosti stříbrného nebo zlata. Má tato změna vliv?
Odpověď **: ne**. Záruky poskytované [úrovněmi odolnosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Service Fabric budou i nadále fungovat i po této změně. Pokud požadujete izolaci fyzického hardwaru z jiných důvodů, možná budete muset provést jednu z výše uvedených akcí. 
