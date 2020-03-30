---
title: Bezpečnostní okno pro zákazníky pro Microsoft Azure
description: Technický přehled customer lockbox pro Microsoft Azure, který poskytuje kontrolu nad přístupem poskytovatele cloudu, když Microsoft může potřebovat přístup k zákaznickým datům.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561965"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Bezpečnostní okno pro zákazníky pro Microsoft Azure

> [!NOTE]
> Chcete-li tuto funkci používat, vaše organizace musí mít [plán podpory Azure](https://azure.microsoft.com/support/plans/) s minimální úrovní **vývojáře**.

Customer Lockbox pro Microsoft Azure poskytuje zákazníkům rozhraní, které může kontrolovat a schvalovat nebo zamítat žádosti o přístup k zákaznickým datům. Používá se v případech, kdy technik společnosti Microsoft potřebuje přístup k zákaznickým datům během žádosti o podporu.

Tento článek popisuje, jak jsou požadavky customer lockbox inicializovány, sledovány a uloženy pro pozdější kontroly a audity.

Customer Lockbox je teď obecně dostupný a aktuálně povolený pro přístup ke vzdálené ploše k virtuálním počítačům.

## <a name="workflow"></a>Pracovní postup

Následující kroky popisují typický pracovní postup pro požadavek customer lockbox.

1. Někdo v organizaci má problém s jejich zatížení Azure.

2. Poté, co tato osoba řeší problém, ale nemůže opravit, otevře lístek podpory z [portálu Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Lístek se přiřazuje k pracovníkovi podpory zákazníků Azure.

3. Pracovník podpory Azure zkontroluje žádost o službu a určí další kroky k vyřešení problému.

4. Pokud pracovník podpory nemůže problém vyřešit pomocí standardních nástrojů a telemetrie, dalším krokem je požádat o zvýšená oprávnění pomocí služby přístupu Just-In-Time (JIT). Tento požadavek může být od původního pracovníka podpory. Nebo může být od jiného inženýra, protože problém je eskalován týmu Azure DevOps.

5. Po odeslání žádosti o přístup pomocí Azure Engineer služba Just-In-Time vyhodnotí požadavek s přihlédnutím k faktorům, jako jsou:
    - Rozsah zdroje
    - Zda je žadatel izolovanou identitou nebo používá vícefaktorové ověřování
    - Úrovně oprávnění

    Na základě pravidla JIT může tento požadavek také obsahovat schválení od interních schvalovatelů společnosti Microsoft. Schvalovatel může být například vedoucí zákaznické podpory nebo Správce devops.

6. Pokud požadavek vyžaduje přímý přístup k datům zákazníka, je inicializován požadavek customer lockbox. Například přístup ke vzdálené ploše k virtuálnímu počítači zákazníka.

    Požadavek je nyní v **oznámeném** stavu zákazníka a čeká na schválení zákazníka před udělením přístupu.

7. V organizaci zákazníka uživatel, který má [roli vlastníka](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) pro předplatné Azure obdrží e-mail od společnosti Microsoft, aby je upozornil na nevyřízený požadavek na přístup. Pro požadavky customer lockbox je tato osoba určená schvalovatel.

    Příklad e-mailu:

    ![Azure Customer Lockbox – e-mailové oznámení](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. E-mailové oznámení obsahuje odkaz na okno **Customer Lockbox** na webu Azure Portal. Pomocí tohoto odkazu se určený schvalovatel přihlásí k portálu Azure a zobrazí všechny čekající požadavky, které má jejich organizace pro customer lockbox:

    ![Azure Customer Lockbox – vstupní stránka](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   Požadavek zůstane ve frontě zákazníka po dobu čtyř dnů. Po uplynutí této doby žádost o přístup automaticky vyprší a není udělen žádný přístup k inženýrům společnosti Microsoft.

9. Chcete-li získat podrobnosti o nevyřízené žádosti, určený schvalovatel může vybrat požadavek bezpečnostního boxu z **čekajících požadavků**:

    ![Azure Customer Lockbox – zobrazení čekajícího požadavku](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Určený schvalovatel může také vybrat **ID žádosti o službu** pro zobrazení žádosti o lístek podpory, která byla vytvořena původním uživatelem. Tyto informace poskytují kontext pro to, proč je podpora společnosti Microsoft zapojena a historii ohlášeného problému. Například:

    ![Azure Customer Lockbox – zobrazení žádosti o lístek podpory](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Po kontrole žádosti vybere určený schvalovatel **možnost Schválit** nebo **Odepřít**:

    ![Azure Customer Lockbox – výběr schválit nebo odepřít](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    V důsledku výběru:
    - **Schválit**: Přístup je udělen technikovi společnosti Microsoft. Přístup je udělen na výchozí dobu osmi hodin.
    - **Odepřít**: Žádost o přístup se zvýšeným i zvýšeným i se zvýšeným i dotazem na přístup inženýra společnosti Microsoft je odmítnuta a nebude provedena žádná další akce.

Pro účely auditování jsou akce provedené v tomto pracovním postupu zaznamenány v [protokolech požadavků customer lockbox .](#auditing-logs)

## <a name="auditing-logs"></a>Auditování protokolů

Protokoly customer lockbox jsou uloženy v protokolech aktivit. Na webu Azure Portal vyberte **protokoly aktivit,** chcete-li zobrazit informace o auditování související s požadavky customer lockbox. Můžete filtrovat pro konkrétní akce, například:
- **Odepřít požadavek bezpečnostního schránku**
- **Vytvořit požadavek bezpečnostního schránku**
- **Schválit požadavek bezpečnostního schránku**
- **Vypršení platnosti požadavku bezpečnostního boxu**

Příklad:

![Azure Customer Lockbox – protokoly aktivit](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Podporované služby a scénáře v obecné dostupnosti

Následující služby a scénáře jsou aktuálně v obecné dostupnosti pro customer lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Přístup ke vzdálené ploše k virtuálním počítačům

Customer Lockbox je aktuálně povolen pro požadavky na přístup ke vzdálené ploše k virtuálním počítačům. Podporovány jsou následující úlohy:
- Platforma jako služba (PaaS) – cloudové služby Azure (role webu a role pracovního procesu)
- Infrastruktura jako služba (IaaS) – Windows a Linux (jenom Správce prostředků Azure)
- Škálovací sada virtuálních strojů – Windows a Linux

> [!NOTE]
> Instance IaaS Classic nejsou klientem Uzamčení nejsou podporovány. Pokud máte úlohy spuštěné v instancích IaaS Classic, doporučujeme jim migrovat z klasických modelů nasazení Správce prostředků. Pokyny najdete v [tématu migrace prostředků IaaS podporovaná platformou z klasického do Správce prostředků Azure](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Podrobné protokoly auditu

U scénářů, které zahrnují přístup ke vzdálené ploše, můžete pomocí protokolů událostí systému Windows zkontrolovat akce provedené technikem společnosti Microsoft. Zvažte použití Azure Security Center ke shromažďování protokolů událostí a zkopírování dat do pracovního prostoru pro analýzu. Další informace najdete [v tématu Shromažďování dat v Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Podporované služby a scénáře ve verzi Preview

Následující služby jsou nyní ve verzi preview pro customer lockbox:

- Azure Storage

- Azure SQL DB

- Průzkumník dat Azure

- Virtuální počítače (nyní také pokrývající přístup k výpisům paměti a spravované disky)

- Převody předplatného Azure

Chcete-li povolit customer lockbox pro tyto nabídky náhledu pro vaši organizaci, zaregistrujte se [do customer lockbox pro Azure Public Preview](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Vyloučení

Požadavky bezpečnostního boxu zákazníka se neaktivují v následujících scénářích technické podpory:

- Technik společnosti Microsoft musí provést činnost, která nespadá do standardních operačních postupů. Například obnovit nebo obnovit služby v neočekávaných nebo nepředvídatelných scénářích.

- Inženýr microsoftu přistupuje k platformě Azure jako součást řešení potíží a neúmyslně má přístup k zákaznickým datům. Například tým sítě Azure provádí řešení potíží, které má za následek zachycení paketů na síťovém zařízení. Pokud však zákazník zašifroval data během přenosu, technik nemůže data číst.

## <a name="next-steps"></a>Další kroky

Customer Lockbox je automaticky k dispozici všem zákazníkům, kteří mají [plán podpory Azure](https://azure.microsoft.com/support/plans/) s minimální úrovní **vývojáře**.

Pokud máte vhodný plán podpory, nepotřebujete žádnou akci, kterou byste museli povolit customer lockbox. Požadavky customer lockbox jsou iniciovány technikem společnosti Microsoft, pokud je tato akce potřebná k postupu lístku podpory, který je podán od někoho ve vaší organizaci.
