---
title: Převody Azure Enterprise
description: Popisuje převody Azure EA.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7aa57fa20c3a043cdb210ccd8a5ddbf61323716d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943692"
---
# <a name="azure-enterprise-transfers"></a>Převody Azure Enterprise

Tento článek obsahuje přehled převodů Enterprise.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Převod podnikového účtu do nové registrace

Při převodu účtu se jeho vlastník přenese z jedné registrace do druhé. Do cílové registrace se přesunou všechna související předplatná pod tímto vlastníkem. Převod účtu využijte v případě, že máte více aktivních registrací a chcete přesunout jenom vybrané vlastníky účtů.

Tato část je určená jenom k informativním účelům, protože tuto akci nemůže provést podnikový správce. Pro převod podnikového účtu na novou registraci se vyžaduje žádost o podporu.

Při převodu podnikového účtu do nové registrace mějte na paměti následující body:

- Převádějí se jenom účty, které jsou zadané v požadavku. Pokud zvolíte všechny účty, převedou se všechny.
- Zdrojová registrace zůstane v aktivním nebo prodlouženém stavu. Registraci můžete používat dál, dokud jí nevyprší platnost.

### <a name="prerequisites"></a>Požadavky

K žádosti o převod účtu je potřeba sdělit následující informace:

- Číslo cílové registrace, název účtu a e-mail vlastníka účtu, který se má převést
- U zdrojové registrace uveďte její číslo a převáděný účet.

Další body, na které je potřeba myslet před převodem účtu:

- Cílovou a zdrojovou registraci musí schválit správce EA.
- Pokud převod účtu vašim požadavkům nevyhovuje, můžete zvážit převod registrace.
- Převod účtů přenese všechny služby a předplatná související s konkrétními účty.
- Po dokončení převodu se převedený účet v rámci zdrojové registrace zobrazí jako neaktivní a v cílové registraci naopak jako aktivní.
- Účet zobrazuje koncové datum odpovídající efektivnímu datu převodu zdrojové registrace jako počáteční datum cílové registrace.
- Jakékoliv použití účtu před efektivním datem převodu zůstane v rámci zdrojové registrace.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Převod podnikové registrace do nové registrace

Převod registrace se bere v úvahu v těchto případech:

- Skončilo období záloh pro aktuální registraci.
- Registrace je ve stavu vypršení platnosti nebo prodloužení a dojednává se nová smlouva.
- Máte více registrací a chcete zkombinovat všechny účty a fakturaci v rámci jediné registrace.

Tato část je určená jenom k informativním účelům, protože tuto akci nemůže provést podnikový správce. Žádost o podporu je nutná k přenosu podnikového zápisu na nový, pokud registrace neplatí pro [přenos automatického zápisu](#auto-enrollment-transfer).

Pokud požádáte o převod celé podnikové registrace do jiné registrace, provedou se následující akce:

- Na novou registraci se převedou všechny služby Azure, všechna předplatná, účty, oddělení a celá struktura registrace, včetně všech správců oddělení EA.
- U registrace se nastaví stav _Přenesené_. Přenesená registrace bude k dispozici jenom pro sestavy, které se týkají historie použití.
- Do přenesené registrace už nemůžete přidávat role ani předplatná. Přenesený stav zabraňuje většímu využití při registraci.
- Zbývající zůstatek zálohy na Azure ve smlouvě se ruší, včetně budoucích podmínek.
-    Pokud registrace, ze které přenášíte, má nákupy na rezervované instance, zachová se v registraci zdrojového kódu, ale všechny výhody rezervované pro ostatní instance se budou přenášet v rámci nového zápisu.
-    Jednorázový nákupní poplatek na tržišti a jakékoli měsíční pevné poplatky, které už vznikly na staré registraci, se přenesou do nové registrace. Poplatky za marketplace na základě spotřeby se převedou.

### <a name="effective-transfer-date"></a>Platné datum převodu

Platným datem převodu může být datum, které je stejné nebo následuje po počátečním datu cílové registrace.

Využití zdrojové registrace se odečte od zálohy na Azure nebo se účtuje jako nadlimitní využití. Využití, ke kterému dojde po přenosu platného data přenosu, se přenese na nový zápis a účtuje se.

### <a name="prerequisites"></a>Požadavky

K žádosti o převod registrace je potřeba sdělit následující informace:

- U zdrojové registrace uveďte její číslo.
- U cílové registrace uveďte číslo registrace, do které probíhá převod.
- Platné datum převodu registrace může být stejné datum, jako je počáteční datum cílové registrace, nebo datum pozdější. Zvolené datum nemůže mít vliv na využití již vystavené faktury za nadlimitní využití.

Další body, na které je potřeba myslet před převodem registrace:

- Cílovou a zdrojovou registraci musí schválit správci EA.
- Pokud převod registrace vašim požadavkům nevyhovuje, zvažte možnost převodu účtu.
- Stav zdrojové registrace se změní na Převedeno nesený a bude k dispozici jenom pro účely generování sestav historie využití.

### <a name="auto-enrollment-transfer"></a>Přenos automatického zápisu

Může se stát, že registrace má **přenesený** stav, a to i v případě, že jste neodeslali lístek podpory pro vyžádání přenosu registrace. Stav **přenesených** výsledků z procesu přenosu automatického zápisu. Aby se proces přenosu automatického zápisu provedl během fráze pro obnovení, je potřeba, abyste v nové smlouvě zahrnuli několik položek:

- Předchozí číslo registrace (musí existovat na portálu EA)
- Datum vypršení platnosti čísla předchozí registrace je jeden den před počátečním datem nové smlouvy.
- Nová smlouva má fakturovanou objednávku předplatného Azure, která má aktuální datum nebo datum zahájení platnosti.
- Nový zápis se vytvoří na portálu EA.

Pokud na portálu EA neexistují žádná data o využití mezi předchozím zápisem a novým zápisem, nemusíte vytvářet lístek podpory přenosu.

### <a name="azure-prepayment"></a>Záloha na Azure

Zálohu na Azure nejde převést mezi registracemi. Zůstatky zálohy na Azure jsou smluvně vázány na registraci, kde byly objednány. V rámci převodu účtu nebo registrace se záloha na Azure nepřevádí.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Převody účtů a registrací nemají vliv na služby

Při převodu účtu nebo registrace nedochází k výpadkům. Pokud jsou poskytnuté všechny požadované informace, může převod proběhnout tentýž den jako požadavek.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Přenos předplatného Enterprise na předplatné s průběžnými platbami

Pokud chcete převést předplatné Enterprise na jednotlivé předplatné s průběžnými platbami, musíte na portálu Azure Enterprise vytvořit novou žádost o podporu. Žádost o podporu vytvoříte výběrem možnosti **+ New support request** (+ Nová žádost o podporu) v oblasti **Help and Support** (Nápověda a podpora).

## <a name="change-azure-subscription-or-account-ownership"></a>Změna vlastnictví účtu nebo předplatného Azure

Na portálu Azure EA můžete převádět předplatná od jednoho vlastníka účtu k jinému. Další informace najdete v části [Změna vlastnictví účtu nebo předplatného Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Účinek převodu předplatného

Pokud se předplatné Azure převádí do účtu ve stejném tenantovi Azure Active Directory, zachovají si všichni uživatelé, skupiny a instanční objekty, které ke správě prostředků používaly [přístup na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md), stále svůj přístup.

Zobrazení uživatelů s přístupem RBAC k předplatnému:

1. Na webu Azure Portal otevřete **Předplatná**.
2. Vyberte předplatné, které chcete zobrazit, a pak vyberte **Řízení přístupu (IAM)** .
3. Vyberte **Přiřazení rolí**. Na stránce Přiřazení rolí se zobrazí seznam všech uživatelů, kteří mají k předplatnému přístup RBAC.

Pokud se předplatné převede do účtu jiného tenanta Azure AD, tak všichni uživatelé, skupiny a instanční objekty, které měly ke správě prostředků přístup [RBAC](../../role-based-access-control/overview.md), tento přístup _ztratí_. I když neexistuje přístup RBAC, může být přístup k předplatnému dostupný prostřednictvím bezpečnostních mechanismů, jako je například:

- Certifikáty pro správu, které udělují uživateli oprávnění správce prostředků předplatného. Další informace najdete v tématu o [vytvoření a nahrání certifikátu pro správu Azure](../../cloud-services/cloud-services-certs-create.md).
- Přístupové klíče pro služby, jako je Storage. Další informace najdete v tématu [Přehled účtu Azure Storage](../../storage/common/storage-account-overview.md).
- Oprávnění pro vzdálený přístup ke službám, jako je Azure Virtual Machines.

Pokud příjemce potřebuje omezit přístup k prostředkům Azure, měl by zvážit aktualizaci tajných klíčů přidružených k dané službě. Většinu prostředků můžete aktualizovat takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra vyberte **Všechny prostředky**.
3. Vyberte prostředek.
4. Na stránce prostředku vyberte **Nastavení**. Tady se můžete podívat na stávající tajné klíče nebo je můžete aktualizovat.

## <a name="next-steps"></a>Další kroky

- Pokud potřebujete pomoc při řešení potíží s portálem Azure EA, přečtěte si téma [Řešení potíží s přístupem k portálu Azure EA](ea-portal-troubleshoot.md).