---
title: Prostředí virtuálního klienta Windows (preview) – Azure
description: Základní prvky prostředí virtuálního klienta Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: c503f665e3e2f99cd59f207c3fc4823b927eaac3
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318446"
---
# <a name="windows-virtual-desktop-environment-preview"></a>Prostředí virtuálního klienta Windows (Preview)

Virtuální Desktop Windows (preview) je služba, která poskytuje uživatelům snadný a bezpečný přístup k jejich virtualizované desktopy a aplikace RemoteApp. Toto téma vám sdělí něco o obecnou strukturu prostředí virtuálního klienta Windows.

## <a name="tenants"></a>Tenanti

Primární rozhraní pro správu prostředí virtuálního klienta Windows je tenant virtuální plochy Windows. Každý tenant virtuální plochy Windows musí být přidruženy s Azure Active Directory, který obsahuje uživatele, kteří se přihlásit k prostředí. Z klienta Windows virtuálního klienta můžete začít vytvářet fondy hostitel pro spouštění úloh vašich uživatelů.

## <a name="host-pools"></a>Fondy hostitele

Hostitel fond je kolekce virtuálních počítačů s Azure, které zaregistrujte se k virtuálnímu klientovi Windows jako hostitele relace při spuštění agenta virtuální plochy Windows. Všechny relace hostování virtuálních počítačů ve fondu hostitele by měl pocházet ze stejné image, konzistentní uživatelské prostředí.

Fond hostitele může být jeden ze dvou typů:

- Osobní, kde je každý hostitel relace je přiřazena k jednotlivým uživatelům.
- Ve fondu, ve kterém relace hostitele může přijímat připojení z každý uživatel oprávnění pro skupinu aplikací v rámci fondu hostitele.

Nastavit další vlastnosti na hostitele fondu změnit chování Vyrovnávání zatížení, kolik relací může trvat každý hostitel relací, a co uživatel způsobů, jak relace hostitele v hostiteli fondu přihlášení relace jejich virtuální plochy Windows. Můžete řídit prostředky publikované aplikace skupinám uživatelů.

## <a name="app-groups"></a>Skupiny aplikací

Skupinu aplikací je logické seskupení aplikace nainstalované na hostitelích relace ve fondu hostitele. Skupinu aplikací může být jeden ze dvou typů:

- Vzdálená aplikace RemoteApp, kam uživatelé přístup aplikace RemoteApp jednotlivě vybrat a publikovat do skupiny aplikací
- Desktop, kde uživatelé přístup k celé ploše

Ve výchozím nastavení se skupina aplikace klasické pracovní plochy (s názvem "Desktop skupiny aplikací") vytvoří automaticky pokaždé, když vytváříte fond hostitele. Kdykoli lze odebrat tuto skupinu aplikací. Však nelze vytvořit jinou skupinu desktopovou aplikaci ve fondu hostitele existuje skupina aplikace klasické pracovní plochy. K publikování aplikace RemoteApp, musíte vytvořit skupinu aplikací RemoteApp. Můžete vytvořit více skupin aplikací RemoteApp scénářů jinému pracovnímu procesu. Různým skupinám aplikace RemoteApp může také obsahovat překrývající se aplikace RemoteApp.

K publikování materiálů pro uživatele, je nutné jim přiřadit do skupin aplikací. Při přiřazování uživatelů do skupiny aplikací, vezměte v úvahu následující věci:

- Uživatele nelze přiřadit skupinu aplikace klasické pracovní plochy a aplikace skupiny Vzdálená aplikace RemoteApp ve stejném fondu hostitele.
- Uživateli lze přiřadit k několika skupinám aplikace v rámci stejného hostitele fondu a jejich kanál bude nahromadění obě skupiny aplikací.

## <a name="tenant-groups"></a>Skupiny tenanta

Virtuální plochy Windows je tenant virtuální plochy Windows, ve kterém se stane většinu nastavení a konfigurace. Tenant virtuální plochy Windows obsahuje fondy hostitele, skupiny aplikací a přiřazení uživatele skupiny aplikací. Může však být určitých situacích, kdy potřebujete ke správě více tenantů virtuální plochy Windows najednou, zejména v případě, že jste poskytovatel cloudových služeb (CSP) nebo partnerský server pro hostování. V těchto situacích můžete použít vlastní skupinu tenanta virtuální plochy Windows umístit jednotlivých tenantů zákazníků virtuální plochy Windows a centrálně spravujte přístup. Pokud spravujete pouze jednoho tenanta virtuální plochy Windows, ale neplatí koncepce skupiny tenanta a můžete pokračovat k provozování a správě vašeho tenanta, který existuje ve výchozí skupině tenanta.

## <a name="end-users"></a>Koncoví uživatelé

Po přiřazení uživatelů k jejich skupiny aplikací, můžete připojit k nasazení virtuální plochy Windows s některý z klientů virtuální plochy Windows.

## <a name="next-steps"></a>Další postup

Další informace o Delegovaný přístup a přiřazení role u uživatelů v [Delegovaný přístup v virtuální plochy Windows](delegated-access-virtual-desktop.md).

Informace o nastavení klientů Windows virtuální plochy najdete v tématu [vytvořit tenanta v virtuální plochy Windows](tenant-setup-azure-active-directory.md).

Zjistěte, jak se připojit k virtuálnímu klientovi Windows, naleznete v následujících článcích:

- [Připojte se ke klientovi vzdálené plochy na Windows 7 a Windows 10](connect-windows-7-and-10.md)
- [Připojení k webovému klientovi virtuálního klienta Windows](connect-web.md)
