---
title: Ušetřit peníze předem platebním pro virtuální počítače Azure – Azure | Microsoft Docs
description: Další informace o vyhrazenou instanci virtuálního počítače Azure se uložit na náklady na virtuální počítače.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 7d85d3500014962a90ae596598332a95f4aa557e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí virtuálního počítače 
Vyhrazená instance virtuálních počítačů umožňují předem platí za jeden rok nebo tři roky výpočetní kapacitu a získat tak slevu na virtuálních počítačích, které používáte. Významně snižuje náklady na virtuální počítač – až 72 procent na průběžnými platbami ceny – s odběru splácených jeden rok nebo tři roky. Vyhrazená instance virtuálních počítačů je fakturační slevu a nemá vliv na stav modulu runtime virtuálních počítačů.

Můžete si koupit vyhrazenou instanci virtuálního počítače [portál Azure](https://aka.ms/reservations). Další informace najdete v tématu [zaplatit předem pro virtuální počítače a uložit peníze s vyhrazenou instancí virtuálního počítače](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>Proč by měl koupit vyhrazenou instanci virtuálního počítače?
Pokud máte virtuální počítače spouštěné na dlouhou dobu, nákup vyhrazenou instanci virtuálního počítače poskytuje nejlepší efektivní cenu. Například pokud spustíte nepřetržitě čtyři instancí standardní D2 v oblasti západní USA, bez rezervace budou se vám účtovat platím jako přejděte jste tempem. Pokud si koupíte vyhrazenou instanci virtuálního počítače pro ty čtyři virtuální počítače, virtuální počítače okamžitě využívat výhody fakturace. Budou se už účtovat podle platím jako jste přejděte sazeb. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>Jaké poplatky nenajdete vyhrazenou instanci virtuálního počítače?
Rezervace se vztahuje pouze na poplatky infrastruktury virtuálního počítače pro virtuální počítače Windows nebo Linux. Rezervace nepopisuje další poplatky za úložiště, sítě nebo softwaru. Pro virtuální počítače s Windows, může zahrnovat Windows s náklady na licencování [Azure hybridní Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>Kdo je vhodné k nákupu vyhrazenou instanci virtuálního počítače?
S těmito typy předplatných Azure zákazníků můžete zakoupit vyhrazenou instanci virtuálního počítače:
-   Enterprise smlouvy typu nabídky předplatného (MS-AZR - 0017P).
-   [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) typu nabídky předplatného (MS-AZR - 003 P).
Musí být v roli "Vlastník" v předplatném koupit vyhrazenou instanci. K nákupu rezervace v podnikového zápisu, musí správce podnikové sítě povolte rezervace nákupy v portálu EA ve výchozím nastavení je povoleno nastavení.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>Jak se fakturuje nákup vyhrazenou instancí virtuálního počítače?
Zakoupení rezervace je zodpovědné za platby vázáno k předplatnému. Pokud máte předplatné Enterprise, náklady na rezervace je odečtena od vaší vyrovnávání peněžních závazků. Pokud vaše vyrovnávání peněžních závazků nezahrnuje náklady na rezervace, se fakturuje Nadlimitní události úrovně.
Pokud máte předplatné průběžnými platbami, platební karty, které máte na váš účet se fakturuje okamžitě. Pokud jste se účtují podle faktury, uvidíte poplatků na vaší další faktury.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>Jak se použije zakoupené slevu vyhrazenou instanci virtuálního počítače?
Diskontní vyhrazenou instanci virtuálního počítače se vztahuje na virtuální počítače, které odpovídají atributy, které jste vybrali při nákupu rezervace. Atributy zahrnují oboru, kde je spuštěn odpovídající virtuálních počítačů. Například pokud chcete slevu vyhrazenou instanci virtuálního počítače pro standardní D2 čtyři virtuální počítače v oblasti západní USA, vyberte předplatné, kde běží virtuálních počítačů. Pokud virtuální počítače jsou spuštěné v různých předplatných v rámci vaší registrace účtu, zvolte rozsah jako sdílený. Sdílené oboru umožňuje slevu rezervace má být použita ve předplatných.
Po zakoupení vyhrazenou instanci virtuálního počítače, můžete změnit rozsah. Chcete-li změnit obor, naleznete v dokumentaci k o tom, jak spravovat rezervace.

Rezervace slevu platí jenom pro virtuální počítače v rámci předplatných s enterprise nebo nabídku průběžných plateb typy. Virtuální počítače běžící na předplatné s jinými typy nabídka neobdrží slevu rezervace. Pro podnikové registrace předplatného typu enterprise vývoje/testování nejsou vhodné pro vyhrazenou instanci výhody.

Jak ovlivňuje rezervace fakturace virtuálního počítače je vysvětleno v [pochopení aplikace rezervace fakturace benefit](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reservation-term-expires"></a>Co se stane, když vyprší platnost termín rezervaci?
Na konci období rezervace fakturační slevu vyprší a infrastruktury virtuálního počítače se fakturuje za platím jako jste přejděte cenu. Rezervace není automatického obnovení. Chcete-li pokračovat, získávání fakturační slevu, je nutné zakoupit novou vyhrazenou instanci virtuálního počítače. 

## <a name="sizes-and-regional-availability"></a>Velikosti a místní dostupnosti
Rezervace jsou k dispozici pro většinu velikosti virtuálních počítačů s několika výjimkami:
- Velikosti virtuálních počítačů Preview – jakékoli velikosti, která je ve verzi preview nejsou k dispozici pro nákup vyhrazenou instanci virtuálního počítače.
- Cloudy – vyhrazenou instancí virtuálního počítače nejsou k dispozici pro nákup v oblastech Azure US Government, Německu nebo Číně. 
- Nedostatečné kvótu – A vyhrazenou instanci virtuálního počítače, jejímž oborem jsou v rámci jednoho předplatného musí mít virtuální procesory kvóty, které jsou k dispozici v rámci předplatného pro nové RI. Například pokud cílové předplatné má kvótu 10 Vcpu pro řadu D-Series, pak si nelze zakoupit vyhrazenou instanci virtuálního počítače pro 11 Standard_D1 instance. Kontrola kvóty pro rezervace zahrnuje virtuální počítače nasazené v rámci předplatného. Pokud například předplatné má kvótu 10 Vcpu pro řadu D-Series. Pokud toto předplatné má dvě instance standard_D1 nasadit, můžete koupit instanci vyhrazený počítač pro 10 standard_D1 instancí v tomto předplatném. 
- Omezení kapacity – ve výjimečných případech Azure omezení zakoupení nové rezervace pro podmnožinu velikostí virtuálních počítačů z důvodu nedostatku kapacity v oblasti.

## <a name="next-steps"></a>Další postup
Spuštění ukládání na virtuálních počítačích prostřednictvím zakoupení [vyhrazenou instanci virtuálního počítače](https://go.microsoft.com/fwlink/?linkid=861721). 

Další informace o vyhrazenou instancí virtuálního počítače, naleznete v následujících článcích.

- [Předem pro virtuální počítače s instancí vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa instancí vyhrazený virtuální počítač](billing-manage-reserved-vm-instance.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci virtuálního počítače](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
