---
title: Prostředí virtuálních počítačů s Windows – Azure
description: Seznamte se se základními prvky prostředí virtuálních počítačů s Windows, jako jsou fondy hostitelů a skupiny aplikací.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a07dd1791b539ea44fcbab250aa9c227ee1705
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88002616"
---
# <a name="windows-virtual-desktop-environment"></a>Prostředí Windows Virtual Desktop

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/environment-setup-2019.md).

Virtuální plocha Windows je služba, která uživatelům poskytuje snadný a zabezpečený přístup ke svým virtualizovaným plochám a aplikacím RemoteApp. V tomto tématu se dozvíte další informace o obecné struktuře prostředí virtuálních počítačů s Windows.

## <a name="host-pools"></a>Fondy hostitelů

Fond hostitelů je kolekce virtuálních počítačů Azure, které se při spuštění agenta virtuálních počítačů s Windows zaregistrují na virtuální plochu Windows jako hostitelé relace. Všechny virtuální počítače hostitele relace ve fondu hostitelů by měly být ve stejném obrázku pro konzistentní uživatelské prostředí zdrojové.

Fond hostitelů může být jeden ze dvou typů:

- Osobní, kde je každý hostitel relace přiřazen k jednotlivým uživatelům.
- Ve fondu, kde můžou hostitelé relace přijímat připojení libovolného uživatele, který je v rámci fondu hostitelů autorizovaný, do skupiny aplikací.

Můžete nastavit další vlastnosti fondu hostitelů, abyste změnili jeho chování vyrovnávání zatížení, kolik relací může trvat a co může uživatel dělat pro hostitele relací ve fondu hostitelů v době, kdy se přihlásil k relacím virtuálních klientů Windows. Prostředky publikované uživatelům můžete řídit prostřednictvím skupin aplikací.

## <a name="app-groups"></a>Skupiny aplikací

Skupina aplikací je logické seskupení aplikací nainstalovaných v hostitelích relací ve fondu hostitelů. Skupina aplikací může být jedním ze dvou typů:

- Vzdálená aplikace RemoteApp, kde uživatelé přistupují k aplikacím RemoteApp, které vyberete jednotlivě a publikujete do skupiny aplikací
- Plocha, kde uživatelé přistupují k celé ploše

Ve výchozím nastavení se při každém vytvoření fondu hostitelů automaticky vytvoří skupina desktopových aplikací (s názvem "skupina desktopových aplikací"). Tuto skupinu aplikací můžete kdykoli odebrat. V rámci fondu hostitelů ale nemůžete vytvořit jinou skupinu desktopových aplikací, zatímco existuje skupina desktopových aplikací. Chcete-li publikovat vzdálené aplikace RemoteApp, je nutné vytvořit skupinu aplikací RemoteApp. Můžete vytvořit několik skupin aplikací RemoteApp, které budou vyhovovat různým pracovním scénářům. Různé skupiny aplikací RemoteApp můžou také obsahovat překrývající se vzdálené aplikace RemoteApp.

Pokud chcete publikovat prostředky uživatelům, musíte je přiřadit ke skupinám aplikací. Při přiřazování uživatelů ke skupinám aplikací Vezměte v úvahu následující věci:

- Uživatel může být přiřazený ke skupině desktopové aplikace i k skupině aplikací RemoteApp ve stejném fondu hostitelů. Uživatelé ale můžou pro každou relaci spustit jenom jeden typ skupiny aplikací. Uživatelé nemohou současně spustit oba typy skupin aplikací v jedné relaci.
- Uživatel může být přiřazený k několika skupinám aplikací v rámci stejného fondu hostitelů a jejich informační kanál bude akumulací obou skupin aplikací.

## <a name="workspaces"></a>Pracovní prostory

Pracovní prostor je logické seskupení skupin aplikací ve virtuálním počítači s Windows. Každá skupina aplikací virtuálních klientů Windows musí být přidružena k pracovnímu prostoru pro uživatele, aby viděli vzdálené aplikace a počítače, které jsou do nich publikovány.

## <a name="end-users"></a>Koncové uživatele

Po přiřazení uživatelů ke skupinám aplikací se mohou připojit k nasazení virtuálních klientů Windows pomocí libovolného klienta virtuálních klientů Windows.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o delegovaném přístupu a přiřazování rolí uživatelům při [delegovaném přístupu na virtuálním počítači s Windows](delegated-access-virtual-desktop.md).

Informace o tom, jak nastavit fond hostitelů pro virtuální počítače s Windows, najdete v tématu [Vytvoření fondu hostitelů pomocí Azure Portal](create-host-pools-azure-marketplace.md).

Informace o tom, jak se připojit k virtuálnímu počítači s Windows, najdete v jednom z následujících článků:

- [Připojení se systémem Windows 10 nebo Windows 7](connect-windows-7-10.md)
- [Připojení pomocí webového prohlížeče](connect-web.md)
- [Práce s klientem Android](connect-android.md)
- [Připojení s klientem macOS](connect-macos.md)
- [Připojení s klientem iOS](connect-ios.md)