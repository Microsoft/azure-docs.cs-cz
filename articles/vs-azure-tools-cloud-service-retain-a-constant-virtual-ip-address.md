---
title: Jak zachovat konstantní virtuální IP adresu pro cloudovou službu Azure | Dokumentace Microsoftu
description: Zjistěte, jak zajistit, že nedojde ke změně virtuální IP adresa (VIP) Azure cloudové služby.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 348e13c919b85018388c8032ea697c8ad69f70f9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969248"
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Zachovat konstantní virtuální IP adresu pro cloudové služby Azure
Když aktualizujete cloudovou službu, která je hostovaná v Azure, můžete potřebovat k zajištění, že nedojde ke změně virtuální IP adresa (VIP) služby. Většina služeb správu domény používá v systému DNS (Domain Name) pro registraci názvů domén. DNS funguje pouze v případě, že virtuální IP adresa zůstala stejná. Můžete použít **Průvodce publikováním** v nástroje Azure k zajištění, že virtuální IP adresa cloudové služby nedojde ke změně při ho aktualizujete. Další informace o tom, jak pomocí DNS domény správy pro cloudové služby, najdete v části [konfigurace vlastního názvu domény pro cloudovou službu Azure](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Publikovat do cloudového beze změny jeho virtuálních IP adres
Virtuální IP adresa cloudové služby se přidělí, když jste nejprve ji nasadit do Azure v konkrétním prostředí, jako je například produkčním prostředí. Virtuální IP adresa se změní pouze v případě, že je explicitně odstranit nasazení nebo nasazení se implicitně odstranila, proces nasazení aktualizací. Pokud chcete zachovat virtuální IP adresy, nemůže nasazení odstranit a ujistěte se, že Visual Studio nedojde k odstranění nasazení automaticky. 

Můžete zadat nastavení nasazení **Průvodce publikováním**, která podporuje několik možností nasazení. Můžete zadat nové nasazení nebo nasazení aktualizace, který může být přírůstkové nebo souběžných. Oba typy nasazení aktualizace zachovat virtuální IP adresy. Definice těchto různých typů nasazení naleznete v tématu [Průvodce publikováním aplikace Azure](vs-azure-tools-publish-azure-application-wizard.md). Kromě toho můžete řídit, jestli je odstranit předchozí nasazení cloudové služby, pokud dojde k chybě. Pokud tuto možnost nemají nastavený správně, může dojít k nečekané změně virtuální IP adresy.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Aktualizace cloudové služby beze změny jeho virtuálních IP adres
1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure. 

2. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt. V místní nabídce vyberte **publikovat**.

    ![Publikování nabídky](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. V **publikování aplikaci Azure** dialogového okna, vyberte předplatné Azure, do které chcete nasadit. Přihlásit, pokud je nezbytné a vyberte **Další**.

    ![Publikování Azure aplikace přihlašovací stránku](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. Na **obecná nastavení** kartu, ověřte, která název cloudu používat, do které nasazujete, **prostředí**, **konfiguraci sestavení**a **Konfigurace služby** správně.

    ![Publikování kartu společné nastavení aplikace Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. Na **Upřesnit nastavení** kartu, ověřte, že **označení nasazení** a **účtu úložiště** jsou správné. Ověřte, že **odstranit nasazení při selhání** zaškrtávací políčko zaškrtnuto a ověřte, že **nasazení aktualizace** zaškrtávací políčko je zaškrtnuto. Zrušením **odstranit nasazení při selhání** zaškrtávací políčko, zajistíte tím, že VIP není ztraceny, pokud dojde k chybě během nasazení. Výběrem **nasazení aktualizace** zaškrtávací políčko, zajistíte tím, že vaše nasazení neodstranil a že vaše virtuální IP adresy se neztratí při opětovném publikování aplikace. 

    ![Publikování kartu Upřesnit nastavení aplikace Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. Chcete-li dále určit, jak chcete, aby role, které chcete aktualizovat, vyberte **nastavení** vedle **nasazení aktualizace**. Vyberte buď **přírůstkové aktualizace** nebo **Souběžná aktualizace**a vyberte **OK**. Zvolte **přírůstkové aktualizace** aktualizovat každou instanci vaší aplikace, jednu po druhé, tak, aby aplikace byla vždy dostupná. Zvolte **Souběžná aktualizace** aktualizovat všechny instance aplikace ve stejnou dobu. Aktualizuje se současně je rychlejší, ale vaše služba nemusí být k dispozici během procesu aktualizace. Až budete hotovi, vyberte **Další**.

    ![Publikovat stránku nastavení nasazení aplikace Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. V **publikování aplikaci Azure** dialogu **Další** až **Souhrn** zobrazí se stránka. Ověřte nastavení a pak vyberte **publikovat**.
   
    ![Publikovat stránku Souhrn aplikací Azure](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Další postup
- [Pomocí sady Visual Studio Azure Průvodce publikováním aplikace](vs-azure-tools-publish-azure-application-wizard.md)

