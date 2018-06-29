---
title: Jaké jsou vyhrazené instancemi Azure? | Dokumenty Microsoft
description: Další informace o vyhrazenou instancí virtuálních počítačů Azure a ceny uložte na náklady na virtuální počítače a získat nejlepší efektivní ceny virtuálních počítačů.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 12fc88596b4283b6f809575328ab801704cc1c8d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063793"
---
# <a name="what-are-azure-reserved-vm-instances"></a>Jaké jsou vyhrazená instance virtuálních počítačů Azure?
[Vyhrazenou instancí virtuálních počítačů v Azure](https://azure.microsoft.com/pricing/reserved-vm-instances) pomáhá šetřit peníze předem platebním pro jeden rok nebo tři roky výpočetní kapacitu díky tomu můžete získat tak slevu na virtuálních počítačích můžete použít. Vyhrazená instance Azure může výrazně snížit náklady na virtuální počítač – až 72 procent na průběžnými platbami ceny – s odběru splácených jeden rok nebo tři roky. Vyhrazená instance poskytnout fakturační slevu a neovlivní stav modulu runtime virtuálních počítačů.

Vyhrazená instance (RI) můžete zakoupit [portál Azure](https://aka.ms/reservations). Další informace najdete v tématu [zaplatit předem pro virtuální počítače a uložit peníze s vyhrazenou instancí](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Proč by měl koupit vyhrazenou instanci?
Pokud máte virtuální počítače spouštěné na dlouhou dobu, nákup vyhrazenou instanci vám dává možnost nákladově nejefektivnější. Například pokud spustíte nepřetržitě čtyři instance standardní D2 virtuálního počítače, v oblasti západní USA, bez vyhrazenou instanci budou se vám účtovat průběžnými platbami tempem. Pokud si koupíte vyhrazenou instanci pro ty čtyři virtuální počítače, virtuální počítače okamžitě využívat výhody fakturace. Budou se už účtovat průběžnými platbami tempem. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Co účtuje nemá titulní vyhrazenou instanci?
Vyhrazená instance se vztahuje pouze na poplatky infrastruktury virtuálního počítače pro virtuální počítače Windows nebo Linux. Vyhrazená instance nepopisuje další poplatky za úložiště, sítě nebo softwaru. Pro virtuální počítače s Windows, může zahrnovat Windows s náklady na licencování [Azure hybridní Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Kdo je vhodné k nákupu vyhrazenou instanci?
S těmito typy předplatných Azure zákazníků můžete zakoupit vyhrazenou instanci:
-   Enterprise smlouvy typu nabídky předplatného (MS-AZR - 0017P).
-   [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) typu nabídky předplatného (MS-AZR - 003 P). Musí být v roli "Vlastník" v předplatném koupit vyhrazenou instanci. K nákupu vyhrazenou instancí v podnikového zápisu, musí správce podnikové sítě povolte vyhrazenou instanci nákupy EA portálu. Ve výchozím nastavení je toto nastavení povoleno.
-   Cloud Solution Provider (CSP) partnery můžete použít portál Azure nebo [Partnerské centrum](https://docs.microsoft.com/partner-center/azure-reservations) přikoupení vyhrazenou instancí.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Jak se fakturuje nákup vyhrazenou instanci?
Vyhrazená instance nákupu je zodpovědné za platby vázáno k předplatnému. Pokud máte předplatné Enterprise, náklady na vyhrazené instanci bude odečtena z vaší vyrovnávání peněžních závazků. Pokud vaše vyrovnávání peněžních závazků nezahrnuje náklady na vyhrazené instanci, se fakturuje Nadlimitní události úrovně.
Pokud máte předplatné průběžnými platbami, platební karty, které máte na váš účet se fakturuje okamžitě. Pokud jste se účtují podle faktury, uvidíte poplatků na vaší další faktury.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>Jak se použije slevu zakoupené vyhrazenou instanci?
Vyhrazená instance slevu se vztahuje na virtuální počítače, které odpovídají atributy, které jste vybrali při nákupu vyhrazenou instanci. Atributy zahrnují oboru, kde je spuštěn odpovídající virtuálních počítačů. Například pokud chcete použít vyhrazenou instanci slevy pro čtyři standardní D2 virtuální počítače v oblasti západní USA, vyberte předplatné, kde běží virtuálních počítačů. Pokud virtuální počítače jsou spuštěné v různých předplatných v rámci vaší registrace účtu, zvolte rozsah jako sdílený. Sdílené oboru umožňuje slevu vyhrazenou instanci má být použita ve předplatných. Po zakoupení vyhrazenou instanci, můžete změnit rozsah. Chcete-li změnit obor, [spravovat vyhrazená instance v Azure](billing-manage-reserved-vm-instance.md).

Vyhrazená instance slevu platí jenom pro virtuální počítače přidružené organizace nebo typy předplatného průběžné platby. Virtuální počítače běžící na předplatné s jinými typy nabídka neobdrží slevu vyhrazenou instanci. Pro podnikové registrace předplatného typu enterprise vývoje/testování nejsou vhodné pro vyhrazenou instanci výhody.

Abyste lépe pochopili, jak vyhrazenou instanci má vliv na vaši fakturaci virtuálního počítače, najdete v části [pochopit, jak se použije slevu vyhrazenou instanci](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>Co se stane, když vyprší platnost termín vyhrazenou instanci?
Na konci období vyhrazenou instanci fakturační slevu vyprší a infrastruktury virtuálního počítače se fakturuje za platím jako jste přejděte cenu. Azure vyhrazenou instancí není automatického obnovení. Chcete-li pokračovat, získávání fakturační slevu, je nutné zakoupit novou vyhrazenou instanci. 

## <a name="sizes-and-regional-availability"></a>Velikosti a místní dostupnosti
Vyhrazená instance jsou k dispozici pro většinu velikosti virtuálních počítačů s několika výjimkami:
- Virtuální počítače ve verzi Preview – všechny řady virtuálních počítačů nebo velikost, která je ve verzi preview nejsou k dispozici pro vyhrazené instanci nákupu.
- Cloudy – vyhrazená instance nejsou k dispozici pro nákup v oblastech Azure US Government, Německu nebo Číně. 
- Nedostatečné kvótu – vyhrazené instanci, která je omezená na v rámci jednoho předplatného musí mít virtuální procesory kvóty, které jsou k dispozici v rámci předplatného pro nové RI. Například pokud cílové předplatné má kvótu 10 Vcpu pro D-Series, pak si nelze zakoupit vyhrazenou instanci pro 11 Standard_D1 instance. Kontrola kvóty pro vyhrazené instance zahrnuje virtuální počítače nasazené v rámci předplatného. Například pokud předplatné má kvótu 10 Vcpu pro D-Series a má dvě instance standard_D1 nasazení, pak můžete koupit vyhrazenou instanci pro 10 standard_D1 instancí v tomto předplatném. 
- Omezení kapacity – ve výjimečných případech Azure omezení velikosti zakoupení nové vyhrazené instance pro podmnožinu virtuálního počítače z důvodu nedostatku kapacity v oblasti.

## <a name="next-steps"></a>Další postup
Spuštění ukládání na virtuálních počítačích prostřednictvím zakoupení [vyhrazenou instanci Azure](https://go.microsoft.com/fwlink/?linkid=861721). 

Další informace o vyhrazenou instancí, naleznete v následujících článcích:

- [Jaké jsou vyhrazená instance virtuálních počítačů Azure?](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Spravovat vyhrazená instance v Azure](billing-manage-reserved-vm-instance.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)
- [Vyhrazená instance v programu partnera Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
