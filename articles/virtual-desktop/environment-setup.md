---
title: Prostředí Virtuální plochy Windows – Azure
description: Základní prvky prostředí virtuální plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127919"
---
# <a name="windows-virtual-desktop-environment"></a>Prostředí Windows Virtual Desktop

Windows Virtual Desktop je služba, která poskytuje uživatelům snadný a bezpečný přístup k jejich virtualizované plochy a RemoteApps. Toto téma vám řekne něco víc o obecné struktuře prostředí Windows Virtual Desktop.

## <a name="tenants"></a>Tenanti

Klient virtuální plochy Windows je primární rozhraní pro správu prostředí Virtuální plochy systému Windows. Každý klient virtuální plochy Windows musí být přidružený ke službě Azure Active Directory obsahující uživatele, kteří se přihlásí k prostředí. Z klienta Virtuální plochy Windows můžete začít vytvářet fondy hostitelů pro spouštění úloh uživatelů.

## <a name="host-pools"></a>Hostitelské fondy

Fond hostitelů je kolekce virtuálních počítačů Azure, které se registrují na virtuální ploše Windows jako hostitelé relací při spuštění agenta Virtuální plochy Windows. Všechny virtuální počítače hostitele relace ve fondu hostitelů by měly pocházet ze stejné bitové kopie pro konzistentní uživatelské prostředí.

Fond hostitelů může být jedním ze dvou typů:

- Osobní, kde je každý hostitel relace přiřazen jednotlivým uživatelům.
- Sdružený, kde hostitelé relací mohou přijímat připojení od libovolného uživatele oprávněného ke skupině aplikací v rámci fondu hostitelů.

Můžete nastavit další vlastnosti ve fondu hostitelů a změnit jeho chování vyrovnávání zatížení, počet relací, které může každý hostitel relace provést, a co může uživatel udělat pro hostitele relací ve fondu hostitelů při přihlášení k relacím virtuální plochy systému Windows. Můžete řídit prostředky publikované uživatelům prostřednictvím skupin aplikací.

## <a name="app-groups"></a>Skupiny aplikací

Skupina aplikací je logické seskupení aplikací nainstalovaných na hostitelích relací ve fondu hostitelů. Skupina aplikací může být jedním ze dvou typů:

- RemoteApp, kde uživatelé přistupují k aplikacím RemoteApps, které jednotlivě vyberete a publikujete ve skupině aplikací
- Plocha, kde uživatelé přistupují k celé ploše

Ve výchozím nastavení se skupina aplikací klasické pracovní plochy (s názvem "Skupina aplikací pro stolní počítače") automaticky vytvoří při každém vytvoření fondu hostitelů. Tuto skupinu aplikací můžete kdykoli odebrat. V hostitelském fondu však nelze vytvořit jinou skupinu aplikací klasické pracovní plochy, dokud existuje skupina aplikací klasické pracovní plochy. Chcete-li publikovat služby RemoteApps, musíte vytvořit skupinu aplikací RemoteApp. Můžete vytvořit více skupin aplikací RemoteApp, které budou přizpůsobeny různým pracovním scénářům. Různé skupiny aplikací RemoteApp mohou také obsahovat překrývající se aplikace RemoteApps.

Chcete-li publikovat prostředky uživatelům, musíte je přiřadit ke skupinám aplikací. Při přiřazování uživatelů ke skupinám aplikací zvažte následující skutečnosti:

- Uživatel nemůže být přiřazen ke skupině aplikací klasické pracovní plochy i ke skupině aplikací RemoteApp ve stejném fondu hostitelů.
- Uživatele lze přiřadit k více skupinám aplikací v rámci stejného fondu hostitelů a jeho zdrojem bude akumulace obou skupin aplikací.

## <a name="tenant-groups"></a>Skupiny klientů

Ve virtuální ploše windows je klient virtuální plochy Windows, kde se provádí většina instalace a konfigurace. Tenant virtuální plochy Windows obsahuje fondy hostitelů, skupiny aplikací a přiřazení uživatelů skupiny aplikací. Mohou však nastat určité situace, kdy potřebujete spravovat více klientů virtuální ch od Windows najednou, zejména pokud jste poskytovatel cloudových služeb (CSP) nebo hostitelský partner. V těchto situacích můžete použít vlastní skupinu klientů virtuální plochy windows umístit každého z klientů virtuální plochy zákazníků windows virtuální plochy a centrálně spravovat přístup. Pokud ale spravujete jenom jednoho klienta virtuální plochy Windows, koncept skupiny klientů se nepoužije a můžete pokračovat v provozu a správě klienta, který existuje ve výchozí skupině klientů.

## <a name="end-users"></a>Koncoví uživatelé

Po přiřazení uživatelů do skupin aplikací se mohou připojit k nasazení virtuální plochy Windows s kterýmkoli z klientů Virtuální plochy Windows.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o delegovaném přístupu a o tom, jak přiřadit role uživatelům v [delegovaném přístupu ve Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Informace o nastavení klienta Virtuální plochy Windows najdete [v tématu Vytvoření klienta ve Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Informace o připojení k virtuální ploše Windows naleznete v jednom z následujících článků:

- [Připojení z Windows 10 nebo Windows 7](connect-windows-7-and-10.md)
- [Připojení z webového prohlížeče](connect-web.md)
