---
title: Připojení SCSM ke konektoru pro správu služeb IT
description: Tento článek poskytuje informace o tom, jak SCSM pomocí konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 79706b66dba46253843b1f53a26481170d6ff723
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729626"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>Připojit System Center Service Manager ke konektoru pro správu služeb IT

Tento článek poskytuje informace o tom, jak nakonfigurovat připojení mezi vaší instancí System Center Service Manager a konektorem ITSMC (IT Service Management Connector) v Log Analytics a centrálně spravovat pracovní položky.

Následující části obsahují podrobné informace o tom, jak připojit System Center Service Manager produkt k ITSMC v Azure.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující předpoklady:

- ITSMC je nainstalovaný. Další informace: [Přidání řešení IT Service Management Connector](./itsmc-definition.md).
- Webová aplikace Service Manager (webová aplikace) je nasazená a nakonfigurovaná. [Tady](#create-and-deploy-service-manager-web-app-service)jsou informace o webové aplikaci.
- Hybridní připojení se vytvořilo a nakonfigurovalo. Další informace: [Konfigurace hybridního připojení](#configure-the-hybrid-connection).
- Podporované verze Service Manager: 2012 R2 nebo 2016.
- Role uživatele: [operátor pokročilý](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10))
- V současné době se můžou výstrahy odeslané z Azure Monitor vytvářet v System Center Service Managerch incidentů.

> [!NOTE]
> - Konektor ITSM se může připojit pouze ke cloudovým instancím ServiceNow. Místní instance ServiceNow se v tuto chvíli nepodporují.
> - Aby bylo možné používat vlastní [šablony](./itsmc-definition.md#template-definitions) jako součást akcí, parametr "ProjectionType" v šabloně SCSM by měl být namapován na "IncidentManagement! System. pracovní položka. incident. ProjectionType "

## <a name="connection-procedure"></a>Postup připojení

K připojení instance System Center Service Manager k ITSMC použijte následující postup:

1. V Azure Portal přejít na **všechny prostředky** a vyhledejte **partnera (YourWorkspaceName)** .

2. V části **zdroje dat pracovního prostoru** klikněte na **připojení ITSM**.

    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce, a kliknutím na tlačítko **OK** vytvořte připojení.

> [!NOTE]
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance System Center Service Manager, ke které se chcete připojit pomocí ITSMC.  Tento název použijete později při konfiguraci pracovních položek v této instanci nebo zobrazení podrobné Log Analytics. |
| **Typ partnera**   | Vyberte **System Center Service Manager**. |
| **Adresa URL serveru**   | Zadejte adresu URL Service Manager webové aplikace. Další informace o Service Manager webové aplikace [najdete tady](#create-and-deploy-service-manager-web-app-service).
| **ID klienta**   | Zadejte ID klienta, které jste vygenerovali (pomocí automatického skriptu) pro ověření webové aplikace. Další informace o automatizovaném skriptu [najdete tady.](./itsmc-service-manager-script.md)|
| **Tajný kód klienta**   | Zadejte tajný klíč klienta generovaný pro toto ID.   |
| **Synchronizovat data**   | Vyberte Service Manager pracovní položky, které chcete synchronizovat pomocí ITSMC.  Tyto pracovní položky jsou importovány do Log Analytics. **Možnosti:**  Incidenty, žádosti o změnu.|
| **Rozsah synchronizace dat** | Zadejte počet uplynulých dní, z nichž mají být data. **Maximální limit**: 120 dní. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Když se tato možnost vybere, Log Analytics v podporovaném systému ITSM vytvoří ovlivněné položky konfigurace pro SNS (v případě neexistujícího modelu SNS). **Výchozí**: zakázáno. |

![Připojení k Service Manageru](media/itsmc-connections/service-manager-connection.png)

**Po úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z Service Manager jsou importovány do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici konektoru pro IT služby IT.

- Můžete vytvářet incidenty z výstrah Log Analytics nebo ze záznamů protokolů nebo z výstrah Azure v této instanci Service Manager.

Další informace: [vytvoření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Vytvoření a nasazení služby Service Manager Web App Service

Společnost Microsoft vytvořila Service Manager webovou aplikaci na GitHubu, aby mohla propojit místní Service Manager s ITSMC v Azure.

Pokud chcete pro Service Manager nastavit webovou aplikaci ITSM, postupujte takto:

- **Nasazení webové aplikace** – nasaďte webovou aplikaci, nastavte vlastnosti a proveďte ověření pomocí Azure AD. Webovou aplikaci můžete nasadit pomocí [automatizovaného skriptu](./itsmc-service-manager-script.md) , který vám Microsoft poskytl.
- **Konfigurace hybridního připojení**  -  [Nakonfigurujte toto připojení](#configure-the-hybrid-connection)ručně.

### <a name="deploy-the-web-app"></a>Nasazení webové aplikace
Pomocí automatizovaného [skriptu](./itsmc-service-manager-script.md) nasaďte webovou aplikaci, nastavte vlastnosti a proveďte ověření pomocí Azure AD.

Spusťte skript zadáním následujících požadovaných podrobností:

- Podrobnosti o předplatném Azure
- Název skupiny prostředků
- Umístění
- Podrobnosti Service Manager serveru (název serveru, doména, uživatelské jméno a heslo)
- Předpona názvu webu pro vaši webovou aplikaci
- Obor názvů ServiceBus

Skript vytvoří webovou aplikaci s názvem, který jste zadali (spolu s několika dalšími řetězci pro její jedinečné nastavení). Vygeneruje **adresu URL webové aplikace**, **ID klienta** a **tajný klíč klienta**.

Uložte hodnoty, které použijete při vytváření připojení pomocí ITSMC.

**Ověření instalace webové aplikace**

1. Přejít na **Azure Portal**  >  **prostředky**.
2. Vyberte webovou aplikaci, klikněte na **Nastavení**  >  **aplikace nastavení**.
3. Potvrďte informace o instanci Service Manager, kterou jste zadali v době nasazení aplikace prostřednictvím skriptu.

## <a name="configure-the-hybrid-connection"></a>Konfigurace hybridního připojení

Následující postup slouží ke konfiguraci hybridního připojení, které spojuje instanci Service Manager s ITSMC v Azure.

1. V části **prostředky Azure** Najděte Service Manager webovou aplikaci.
2. Klikněte na **Nastavení**  >  **sítě**.
3. V části **Hybrid Connections** klikněte na **Konfigurovat koncové body hybridního připojení**.

    ![Sítě hybridního připojení](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. V okně **Hybrid Connections** klikněte na **Přidat hybridní připojení**.

    ![Přidat hybridní připojení](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. V okně **přidat Hybrid Connections** klikněte na **vytvořit nové hybridní připojení**.

    ![Nové hybridní připojení](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Zadejte následující hodnoty:

   - **Název koncového bodu**: zadejte název nového hybridního připojení.
   - **Hostitel koncového bodu**: plně kvalifikovaný název domény Management Server Service Manager.
   - **Port koncového bodu**: typ 5724
   - **ServiceBus obor názvů**: použijte existující obor názvů ServiceBus nebo vytvořte nový.
   - **Umístění**: vyberte umístění.
   - **Název**: zadejte název ServiceBus, pokud ho vytváříte.

     ![Hodnoty hybridního připojení](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kliknutím na **OK** zavřete okno **vytvořit hybridní připojení** a začněte vytvářet hybridní připojení.

    Po vytvoření hybridního připojení se zobrazí v okně.

7. Po vytvoření hybridního připojení vyberte připojení a klikněte na **Přidat vybrané hybridní připojení**.

    ![Nové hybridní připojení](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>Konfigurace nastavení naslouchacího procesu

Pomocí následujícího postupu můžete nakonfigurovat nastavení naslouchacího procesu pro hybridní připojení.

1. V okně **Hybrid Connections** klikněte na **Stáhnout Správce připojení** a nainstalujte ho do počítače, na kterém běží System Center Service Manager instance.

    Po dokončení instalace je možnost **správce hybridního připojení uživatelského rozhraní** dostupná v nabídce **Start** .

2. Klikněte na **správce hybridního připojení uživatelské rozhraní** , zobrazí se výzva k zadání přihlašovacích údajů Azure.

3. Přihlaste se pomocí svých přihlašovacích údajů Azure a vyberte předplatné, ve kterém se hybridní připojení vytvořilo.

4. Klikněte na **Uložit**.

Vaše hybridní připojení se úspěšně připojilo.

![hybridní připojení bylo úspěšné.](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Po vytvoření hybridního připojení ověřte a otestujte připojení návštěvou nasazené webové aplikace Service Manager. Než se pokusíte připojit k ITSMC v Azure, ujistěte se, že je připojení úspěšné.

Následující vzorový obrázek ukazuje podrobné informace o úspěšném připojení:

![Test hybridního připojení](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>Další kroky

* [Přehled konektoru ITSM](itsmc-overview.md)
* [Vytváření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)