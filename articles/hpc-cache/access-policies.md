---
title: Použití zásad přístupu v mezipaměti HPC Azure
description: Vytvoření a použití zásad vlastního přístupu pro omezení přístupu klienta k cílům úložiště v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: eb9e71cc8ec463077e3b12b8738203a4945a2eab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471777"
---
# <a name="control-client-access"></a>Řízení přístupu klienta

Tento článek vysvětluje, jak vytvořit a použít vlastní zásady klientského přístupu pro vaše cíle úložiště.

Zásady přístupu klienta určují, jak se klienti mohou připojit k exportům cílů úložiště. Na úrovni hostitele nebo sítě klienta můžete řídit například oprávnění ke čtení a zápisu, jako je například kořenový "squash a přístup pro čtení a zápis.

Zásady přístupu se použijí na cestu oboru názvů, což znamená, že můžete použít různé zásady přístupu pro dva různé exporty v systému úložiště NFS.

Tato funkce je určená pro pracovní postupy, ve kterých potřebujete určit, jak různé skupiny klientů přistupují k cíli úložiště.

Pokud nepotřebujete pro přístup k cíli úložiště jemně odstupňovanou kontrolu, můžete použít výchozí zásady, nebo můžete přizpůsobit výchozí zásady pomocí dalších pravidel. Pokud třeba chcete povolit kořenové "squash pro všechny klienty, kteří se připojují přes mezipaměť, můžete upravit zásadu s názvem **Default** a přidat tak nastavení root" squash.

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

Toto nastavení určuje, jak mezipaměť HPC Azure zpracovává požadavky od kořenového uživatele na klientských počítačích. Pokud je povolená možnost root "squash, budou se uživatelé z klienta při odesílání požadavků přes mezipaměť HPC Azure automaticky mapovat na uživatele bez oprávnění. Zabrání taky klientským žádostem o použití bitů oprávnění set-UID.

Pokud je kořenový "squash zakázaný, požadavek od uživatele root Client (UID 0) se předá do systému úložiště NFS back-end jako kořen. Tato konfigurace může umožňovat nevhodný přístup k souborům.

Nastavení kořenového "squashu pro požadavky klientů může přispět k náhradě vyžadovaného ``no_root_squash`` nastavení v systémech NAS, které se používají jako cíle úložiště. (Další informace o [požadavcích cíle úložiště NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)najdete v tématu.) Může taky zvýšit zabezpečení při použití s cíli úložiště Azure Blob.

Pokud zapnete kořenový "squash, musíte taky nastavit hodnotu uživatele anonymní ID. Portál přijímá celočíselné hodnoty mezi 0 a 4294967295. (Staré hodnoty – 2 a-1 jsou podporované z důvodu zpětné kompatibility, ale nedoporučují se pro nové konfigurace.)

Tyto hodnoty jsou namapovány na konkrétní hodnoty uživatele:

* **-2** nebo **65534** (nikdo)
* **-1** nebo **65535** (bez přístupu)
* **0** (Neprivilegovaný kořen)

Systém úložiště může mít jiné hodnoty se speciálními významy.

## <a name="update-access-policies"></a>Aktualizace zásad přístupu

Zásady přístupu můžete upravit nebo odstranit z tabulky na stránce **zásady klientského přístupu** .

Klikněte na název zásady a otevřete ho pro úpravy.

Pokud chcete zásadu odstranit, zaškrtněte políčko vedle jejího názvu v seznamu a pak klikněte na tlačítko **Odstranit** v horní části seznamu. Nemůžete odstranit zásadu s názvem default.

> [!NOTE]
> Nemůžete odstranit zásadu přístupu, která se používá. Odeberte zásadu ze všech cest oboru názvů, které je obsahují, než se ji pokusíte odstranit.

## <a name="next-steps"></a>Další kroky

* Použijte zásady přístupu v cestách oboru názvů pro vaše cíle úložiště. Přečtěte si [Nastavení agregovaného oboru názvů](add-namespace-paths.md) , abyste se dozvěděli.
