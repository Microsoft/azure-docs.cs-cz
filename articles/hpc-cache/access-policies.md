---
title: Použití zásad přístupu v mezipaměti HPC Azure
description: Vytvoření a použití zásad vlastního přístupu pro omezení přístupu klienta k cílům úložiště v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/28/2020
ms.author: v-erkel
ms.openlocfilehash: 795b194eb7cd31e633128c22ddffe808b32e07da
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802404"
---
# <a name="use-client-access-policies"></a>Použití zásad klientského přístupu

Tento článek vysvětluje, jak vytvořit a použít vlastní zásady klientského přístupu pro vaše cíle úložiště.

Zásady přístupu klienta určují, jak se klienti mohou připojit k exportům cílů úložiště. Na úrovni hostitele nebo sítě klienta můžete řídit například oprávnění ke čtení a zápisu, jako je například kořenový "squash a přístup pro čtení a zápis.

Zásady přístupu se použijí na cestu oboru názvů, což znamená, že můžete použít různé zásady přístupu pro dva různé exporty v systému úložiště NFS.

Tato funkce je určená pro pracovní postupy, ve kterých potřebujete určit, jak různé skupiny klientů přistupují k cíli úložiště.

Pokud nepotřebujete pro přístup k cíli úložiště jemně odstupňovanou kontrolu, můžete použít výchozí zásady, nebo můžete přizpůsobit výchozí zásady pomocí dalších pravidel.

## <a name="create-a-client-access-policy"></a>Vytvoření zásad klientského přístupu

Pomocí stránky **zásady klientského přístupu** v Azure Portal můžete vytvářet a spravovat zásady. <!-- is there AZ CLI for this? -->

[![snímek obrazovky se stránkou zásad klientského přístupu. Je definováno několik zásad a některé jsou rozbaleny, aby se zobrazila pravidla](media/policies-overview.png)](media/policies-overview.png#lightbox)

Každá zásada se skládá z pravidel. Pravidla se aplikují na hostitele v pořadí od nejmenšího rozsahu (od hostitele) po největší (výchozí). První pravidlo, které odpovídá, je použito a pozdější pravidla budou ignorována.

Pokud chcete vytvořit novou zásadu přístupu, klikněte na tlačítko **+ Přidat zásadu přístupu** v horní části seznamu. Dejte nové zásadě přístupu název a zadejte aspoň jedno pravidlo.

![snímek obrazovky s doplňováním okna upravit zásady přístupu s více pravidly Kliknutím na tlačítko OK uložte pravidlo.](media/add-policy.png)

Zbytek této části vysvětluje hodnoty, které můžete v pravidlech použít.

### <a name="scope"></a>Obor

Podmínky oboru a filtr adres společně určují, na kterých klientech má pravidlo vliv.

Použijte je k určení, jestli se pravidlo vztahuje na jednotlivé klienty (hostitele), rozsah IP adres (sítě) nebo na všechny klienty (výchozí).

Vyberte odpovídající hodnotu **oboru** pro pravidlo:

* **Hostitel** – pravidlo se vztahuje na jednotlivé klienty.
* **Síť** – pravidlo se vztahuje na klienty v rozsahu IP adres.
* **Výchozí** – pravidlo platí pro všechny klienty.

Pravidla v zásadách jsou vyhodnocována v tomto pořadí. Až požadavek na připojení klienta odpovídá jednomu pravidlu, ostatní se ignorují.

### <a name="address-filter"></a>Filtr adres

Hodnota **filtru adresy** určuje, kteří klienti budou pravidlo odpovídat.

Pokud nastavíte obor na možnost **Host**, můžete ve filtru zadat pouze jednu IP adresu. Pro **výchozí** nastavení oboru nemůžete do pole **Filtr adres** zadat žádné IP adresy, protože výchozí obor odpovídá všem klientům.

Zadejte IP adresu nebo rozsah adres pro toto pravidlo. K určení rozsahu adres použijte zápis CIDR (příklad: 0.1.0.0/16).

### <a name="access-level"></a>Úroveň přístupu

Nastavte, jaká oprávnění se mají udělit klientům, kteří odpovídají oboru a filtru.

Možnosti jsou **jen pro čtení a zápis**, **jen pro čtení** nebo **k žádnému přístupu**.

### <a name="suid"></a>SUID

Zaškrtněte políčko **SUID** , pokud chcete, aby soubory v úložišti nastavily ID uživatelů při přístupu.

SUID se obvykle používá k dočasnému zvýšení oprávnění uživatele, aby uživatel mohl provádět úkol související s tímto souborem.

### <a name="submount-access"></a>Přístup k připojení

Zaškrtněte toto políčko, pokud chcete, aby zadaní klienti mohli přímo připojit tyto podadresáře exportu.

### <a name="root-squash"></a>Kořenová "squash

Určete, jestli se mají u klientů, kteří splňují toto pravidlo, nastavit "squash rootem.

Tato hodnota umožňuje, abyste na úrovni exportu úložiště povolili kořen "squash. Můžete také [Nastavit kořen "squash na úrovni mezipaměti](configuration.md#configure-root-squash).

Pokud zapnete kořenový "squash, musíte taky nastavit hodnotu uživatel anonymního ID na jednu z těchto možností:

* **-2** (nikdo)
* **65534** (nikdo)
* **-1** (bez přístupu)
* **65535** (bez přístupu)
* **0** (Neprivilegovaný kořen)

## <a name="update-access-policies"></a>Aktualizace zásad přístupu

Zásady přístupu můžete upravit nebo odstranit z tabulky na stránce **zásady klientského přístupu** .

Klikněte na název zásady a otevřete ho pro úpravy.

Pokud chcete zásadu odstranit, zaškrtněte políčko vedle jejího názvu v seznamu a pak klikněte na tlačítko **Odstranit** v horní části seznamu. Nemůžete odstranit zásadu s názvem default.

> [!NOTE]
> Nemůžete odstranit zásadu přístupu, která se používá. Odeberte zásadu ze všech cest oboru názvů, které je obsahují, než se ji pokusíte odstranit.

## <a name="next-steps"></a>Další kroky

* Použijte zásady přístupu v cestách oboru názvů pro vaše cíle úložiště. Přečtěte si [Nastavení agregovaného oboru názvů](add-namespace-paths.md) , abyste se dozvěděli.
