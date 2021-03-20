---
title: Prostředí virtuálních počítačů (Classic) Windows – Azure
description: Základní prvky prostředí virtuálních počítačů s Windows (Classic).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4fa281f6435013e9feb6808aab04ffa69c5f96c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008606"
---
# <a name="windows-virtual-desktop-classic-environment"></a>Prostředí virtuálních klientů Windows (klasické)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../environment-setup.md).

Virtuální plocha Windows je služba, která uživatelům poskytuje snadný a zabezpečený přístup ke svým virtualizovaným plochám a aplikacím RemoteApp. V tomto tématu se dozvíte další informace o obecné struktuře prostředí virtuálních počítačů s Windows.

## <a name="tenants"></a>Tenanti

Tenant virtuálních klientů Windows je primárním rozhraním pro správu prostředí virtuálních počítačů s Windows. Každý tenant virtuálních klientů s Windows musí být přidružený k Azure Active Directory, která obsahuje uživatele, kteří se přihlásí k prostředí. Z tenanta virtuálních klientů Windows můžete začít vytvářet fondy hostitelů pro spouštění úloh vašich uživatelů.

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

- Uživatele nejde přiřadit ke skupině desktopové aplikace a skupině aplikací RemoteApp ve stejném fondu hostitelů.
- Uživatel může být přiřazený k několika skupinám aplikací v rámci stejného fondu hostitelů a jejich informační kanál bude akumulací obou skupin aplikací.

## <a name="tenant-groups"></a>Skupiny tenantů

Ve virtuálním počítači s Windows je tenant virtuálních klientů Windows, kde se většina nastavení a konfigurace stane. Tenant virtuálních klientů Windows obsahuje fondy hostitelů, skupiny aplikací a přiřazení uživatelů skupiny aplikací. Mohou však nastat situace, kdy potřebujete spravovat více tenantů virtuálních klientů Windows najednou, zejména pokud jste poskytovatel cloudových služeb (CSP) nebo hostující partner. V těchto situacích můžete použít vlastní skupinu tenantů virtuálních počítačů s Windows k umístění každého tenanta virtuálních klientů Windows v zákaznících a centrálně spravovat přístup. Pokud ale jenom spravujete jediného tenanta virtuálních klientů s Windows, koncept skupiny tenantů se nepoužije a můžete dál provozovat a spravovat tenanta, který existuje ve výchozí skupině tenantů.

## <a name="end-users"></a>Koncové uživatele

Po přiřazení uživatelů ke skupinám aplikací se mohou připojit k nasazení virtuálních klientů Windows pomocí libovolného klienta virtuálních klientů Windows.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o delegovaném přístupu a přiřazování rolí uživatelům při [delegovaném přístupu na virtuálním počítači s Windows](delegated-access-virtual-desktop-2019.md).

Informace o tom, jak nastavit tenanta pro virtuální počítače s Windows, najdete v tématu [Vytvoření tenanta ve virtuálním počítači s Windows](tenant-setup-azure-active-directory.md).

Informace o tom, jak se připojit k virtuálnímu počítači s Windows, najdete v jednom z následujících článků:

- [Připojení z Windows 10 nebo Windows 7](connect-windows-7-10-2019.md)
- [Připojení z webového prohlížeče](connect-web-2019.md)
