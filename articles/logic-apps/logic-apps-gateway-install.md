---
title: Instalace místní brány dat
description: Než budete moct získat přístup k datům z Azure Logic Apps, Stáhněte a nainstalujte místní bránu dat.
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: a36b9d20fa20df56ec53e090976ea86e689ac74b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322508"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalace místní brány dat pro Azure Logic Apps

Než se budete moct [připojit k místním zdrojům dat z Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md), Stáhněte a nainstalujte místní [bránu dat](https://aka.ms/on-premises-data-gateway-installer) do místního počítače. Tato brána funguje jako most, který poskytuje rychlý přenos dat a šifrování mezi místními zdroji dat a vašimi aplikacemi logiky. Stejnou instalaci brány můžete použít i u jiných cloudových služeb, jako je Power BI, automatizace, Power Apps a Azure Analysis Services. Informace o tom, jak používat bránu s těmito službami, najdete v těchto článcích:

* [Microsoft Power Automatizujte místní bránu dat](/power-automate/gateway-reference)
* [Místní brána dat Power BI Microsoftu](/power-bi/service-gateway-onprem)
* [Místní brána dat Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services místní bránu dat](../analysis-services/analysis-services-gateway.md)

Tento článek ukazuje, jak stáhnout, nainstalovat a nastavit místní bránu dat, abyste mohli přistupovat k místním zdrojům dat z Azure Logic Apps. Další informace o [tom, jak brána pro data funguje](#gateway-cloud-service) , najdete dál v tomto tématu. Další informace o bráně najdete v tématu [co je místní brána](/data-integration/gateway/service-gateway-onprem)? Pokud chcete automatizovat úlohy instalace a správy brány, podívejte se na galerii prostředí PowerShell pro [rutiny prostředí PowerShell pro datagateway](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud nemáte účet Azure s předplatným, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  * Váš účet Azure musí být buď pracovní účet, nebo školní účet, který vypadá nějak takto `username@contoso.com` . Nemůžete použít účty Azure B2B (Guest) ani osobní účty Microsoft, například @hotmail.com nebo @outlook.com .

    > [!NOTE]
    > Pokud jste si zaregistrovali Microsoft 365 nabídku a nezadali jste svoji pracovní e-mailovou adresu, může vaše adresa vypadat jako `username@domain.onmicrosoft.com` . Váš účet je uložený v tenantovi Azure AD. Ve většině případů je hlavní název uživatele (UPN) pro váš účet Azure stejný jako vaše e-mailová adresa.

    Pokud chcete použít [standardní předplatné sady Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) , které je přidružené k účet Microsoft, [vytvořte nejprve tenanta Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) nebo použijte výchozí adresář. Přidejte do adresáře uživatele s heslem a pak mu poskytněte přístup k vašemu předplatnému Azure. Pak se můžete přihlásit při instalaci brány pomocí tohoto uživatelského jména a hesla.

  * Váš účet Azure musí patřit jenom do jednoho [tenanta Azure Active Directory (Azure AD) nebo adresáře](../active-directory/fundamentals/active-directory-whatis.md#terminology). Pro instalaci a správu brány na místním počítači je potřeba použít stejný účet Azure.

  * Když bránu instalujete, přihlásíte se pomocí účtu Azure, který propojí instalaci brány s vaším účtem Azure a jenom s tímto účtem. Nemůžete propojit stejnou instalaci brány mezi několika účty Azure nebo klienty Azure AD.

  * Později v Azure Portal musíte použít stejný účet Azure k vytvoření prostředku brány Azure, který odkazuje na instalaci brány. Můžete propojit jenom jednu instalaci brány a jeden prostředek brány Azure. Váš účet Azure se ale může připojit k různým instalacím brány, které jsou přidružené k prostředku brány Azure. Aplikace logiky pak můžou použít tento prostředek brány v aktivačních událostech a akcích, které mají přístup k místním zdrojům dat.

* Tady jsou požadavky na váš místní počítač:

  **Minimální požadavky**

  *  .NET Framework 4.7.2
  * 64bitová verze systému Windows 7 nebo Windows Server 2008 R2 (nebo novější)

  **Doporučené požadavky**

  * PROCESOR s 8 jádry
  * 8 GB paměti
  * 64. bitová verze systému Windows Server 2012 R2 nebo novější
  * Úložiště SSD (Solid-State Drive) pro zařazování

  > [!NOTE]
  > Brána nepodporuje jádro Windows serveru.

* **Související otázky**

  * Místní bránu dat nainstalujte jenom v místním počítači, nikoli v řadiči domény. Bránu nemusíte instalovat na stejný počítač jako zdroj dat. Pro všechny zdroje dat potřebujete jenom jednu bránu, takže nemusíte bránu instalovat pro každý zdroj dat.

    > [!TIP]
    > Abyste minimalizovali latenci, můžete bránu nainstalovat co nejblíže zdroji dat nebo do stejného počítače, za předpokladu, že máte oprávnění.

  * Nainstalujte bránu do místního počítače, který je v kabelové síti, připojený k Internetu, vždycky zapnutý a nepřejde do režimu spánku. V opačném případě se brána nemůže spustit a výkon se může zpomalit v bezdrátové síti.

  * Pokud plánujete používat ověřování systému Windows, ujistěte se, že jste nainstalovali bránu do počítače, který je členem stejného prostředí služby Active Directory jako vaše zdroje dat.

  * Oblast, kterou vyberete pro instalaci brány, je stejné umístění, které musíte vybrat při pozdějším vytvoření prostředku brány Azure pro vaši aplikaci logiky. Ve výchozím nastavení se jedná o stejné umístění jako váš tenant služby Azure AD, který spravuje váš účet Azure. Umístění však můžete změnit během instalace brány.

  * Pokud aktualizujete instalaci brány, odinstalujte nejprve svou aktuální bránu pro čisticí prostředí.

    V souladu s osvědčeným postupem se ujistěte, že používáte podporovanou verzi. Společnost Microsoft každý měsíc vydává novou aktualizaci místní brány dat a aktuálně podporuje jenom poslední šest verzí pro místní bránu dat. Pokud dochází k problémům s verzí, kterou používáte, zkuste [upgradovat na nejnovější verzi](https://aka.ms/on-premises-data-gateway-installer) , protože váš problém může být vyřešen v nejnovější verzi.

  * Brána má dva režimy: standardní režim a osobní režim, které platí jenom pro Power BI. Na stejném počítači nemůžete mít spuštěnou více než jednu bránu ve stejném režimu.

  * Azure Logic Apps podporuje operace čtení a zápisu prostřednictvím brány. Nicméně tyto operace mají [omezení velikosti datové části](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalace brány dat

1. [Stáhněte a spusťte instalační program brány na místním počítači](https://aka.ms/on-premises-data-gateway-installer).

1. Zkontrolujte minimální požadavky, ponechte výchozí instalační cestu, přijměte podmínky použití a pak vyberte **nainstalovat**.

   ![Kontrola požadavků a přijetí podmínek použití](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Po úspěšné instalaci brány zadejte e-mailovou adresu účtu Azure a pak vyberte **Přihlásit**se, například:

   ![Přihlaste se pomocí pracovního nebo školního účtu.](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Vaše instalace brány se může propojit jenom s jedním účtem Azure.

1. **V tomto počítači vyberte zaregistrovat novou bránu**  >  **Next**. Tento krok zaregistruje instalaci brány pomocí [cloudové služby brány](#gateway-cloud-service).

   ![Registrovat bránu v místním počítači](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Zadejte tyto informace pro instalaci brány:

   * Název brány, který je jedinečný v rámci vašeho tenanta Azure AD
   * Obnovovací klíč, který musí obsahovat alespoň osm znaků, které chcete použít
   * Potvrzení obnovovacího klíče

   ![Zadání informací pro instalaci brány](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Uložte a zachovejte obnovovací klíč na bezpečném místě. Tento klíč budete potřebovat, pokud někdy chcete změnit umístění, přesunout, obnovit nebo převzít instalaci brány.

   Poznamenejte si možnost **Přidat existující cluster brány**, který vyberete při instalaci dalších bran pro [scénáře s vysokou dostupností](#high-availability).

1. Ověřte oblast pro cloudovou službu brány a [Azure Service Bus instanci zasílání zpráv](../service-bus-messaging/service-bus-messaging-overview.md) , kterou používá instalace brány. Ve výchozím nastavení má tato oblast stejné umístění jako tenant Azure AD pro váš účet Azure.

   ![Potvrdit oblast pro službu brány a Service Bus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Pokud chcete přijmout výchozí oblast, vyberte **Konfigurovat**. Pokud však výchozí oblast není ta, která je pro vás nejblíže, můžete změnit oblast.

   *Proč změnit oblast pro instalaci brány?*

   Pokud například chcete snížit latenci, můžete změnit oblast brány na stejnou oblast jako aplikace logiky. Případně můžete vybrat oblast nejbližší k místnímu zdroji dat. Váš *prostředek brány v Azure* a aplikace logiky můžou mít různá umístění.

   1. Vedle aktuální oblasti vyberte **změnit oblast**.

      ![Změna aktuální oblasti brány](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na další stránce otevřete seznam **Vybrat oblast** , vyberte požadovanou oblast a vyberte **Hotovo**.

      ![Vyberte jinou oblast pro službu brány](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Přečtěte si informace v okně poslední potvrzení. V tomto příkladu se používá stejný účet jako Logic Apps, Power BI, Power Apps a automatizace, takže je tato brána dostupná pro všechny tyto služby. Až budete připraveni, vyberte **Zavřít**.

   ![Potvrzení informací o bráně dat](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Nyní [vytvořte prostředek Azure pro instalaci brány](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Kontrolovat nebo upravovat nastavení komunikace

Místní brána dat závisí na [Azure Service Bus zasílání zpráv](../service-bus-messaging/service-bus-messaging-overview.md) pro cloudové připojení a stanovuje odpovídající odchozí připojení k příslušné oblasti Azure přidružené k bráně. Pokud vaše pracovní prostředí vyžaduje, aby provoz prochází přes proxy server nebo bránu firewall pro přístup k Internetu, může toto omezení zabránit místní bráně dat v připojení ke cloudové službě brány a Azure Service Bus zasílání zpráv. Brána má několik nastavení komunikace, která můžete upravit. Další informace najdete v těchto tématech:

* [Úprava nastavení komunikace pro místní bránu dat](/data-integration/gateway/service-gateway-communication)
* [Konfigurace nastavení proxy serveru pro místní bránu dat](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Podpora vysoké dostupnosti

Aby nedocházelo k jednomu bodu selhání pro místní přístup k datům, můžete mít k dispozici několik instalací brány (jenom standardní režim) s každou v jiném počítači a nastavit je jako cluster nebo skupina. Tímto způsobem, pokud je primární brána nedostupná, požadavky na data se směrují do druhé brány atd. Vzhledem k tomu, že na počítač můžete nainstalovat jenom jednu standardní bránu, musíte nainstalovat každou další bránu, která je v clusteru v jiném počítači. Všechny konektory, které pracují s místní bránou dat, podporují vysokou dostupnost.

* Musíte už mít aspoň jednu instalaci brány se stejným účtem Azure jako primární bránu a obnovovací klíč pro tuto instalaci.

* V primární bráně musí být spuštěná aktualizace brány ze listopadu 2017 nebo novější.

Po nastavení primární brány, když přejdete k instalaci jiné brány, vyberte **Přidat do existujícího clusteru brány**, vyberte primární bránu, která je první bránu, kterou jste nainstalovali, a zadejte pro tuto bránu obnovovací klíč. Další informace najdete v tématu [clustery s vysokou dostupností pro místní bránu dat](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Změna umístění, migrace, obnovení nebo převzetí existující brány

Pokud musíte změnit umístění brány, přesunout instalaci brány do nového počítače, obnovit poškozenou bránu nebo převzít vlastnictví existující brány, budete potřebovat obnovovací klíč, který byl k dispozici při instalaci brány.

> [!NOTE]
> Před obnovením brány na počítači, který má původní instalaci brány, musíte nejdřív odinstalovat bránu na tomto počítači. Tato akce odpojí původní bránu.
> Pokud odeberete nebo odstraníte cluster brány pro jakoukoli cloudovou službu, nemůžete tento cluster obnovit.

1. Spusťte instalační program brány na počítači, který má existující bránu.

1. Po otevření instalačního programu se přihlaste pomocí stejného účtu Azure, který jste použili k instalaci brány.

1. Vyberte možnost **migrovat, obnovit nebo převzetí existující brány**  >  **Next**, například:

   ![Vyberte možnost migrace, obnovení nebo převzetí existující brány.](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Vyberte z dostupných clusterů a bran a zadejte obnovovací klíč pro vybranou bránu, například:

   ![Vybrat bránu a zadat obnovovací klíč](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Chcete-li změnit oblast, vyberte možnost **změnit oblast**a vyberte novou oblast.

1. Až budete připraveni, vyberte **Konfigurovat** , aby bylo možné dokončit úlohu.

## <a name="tenant-level-administration"></a>Správa na úrovni tenanta

Aby bylo možné získat přehled o všech místních branách dat v tenantovi Azure AD, můžou se globální správci v tomto tenantovi přihlašovat do centra pro [správu Power Platform](https://powerplatform.microsoft.com) jako správce tenanta a vybrat možnost **brány dat** . Další informace najdete v tématu [Správa na úrovni tenanta pro místní bránu dat](/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Restartování brány

Ve výchozím nastavení se instalace brány na místním počítači spouští jako účet služby systému Windows s názvem "místní služba brány dat". Instalace brány ale používá `NT SERVICE\PBIEgwService` název pro přihlašovací údaje účtu "přihlásit se jako" a má oprávnění "přihlásit jako službu".

> [!NOTE]
> Váš účet služby Windows se liší od účtu používaného pro připojení k místním zdrojům dat a z účtu Azure, který používáte při přihlašování ke cloudovým službám.

Stejně jako u jakékoli jiné služby Windows můžete bránu spouštět a zastavovat různými způsoby. Další informace najdete v tématu [restart místní brány dat](/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Jak brána funguje

Uživatelé ve vaší organizaci mají přístup k místním datům, ke kterým už mají autorizovaný přístup. Než se však tito uživatelé budou moci připojit k místnímu zdroji dat, je třeba nainstalovat a nastavit místní bránu dat. Správce je obvykle osoba, která instaluje a nastavuje bránu. Tyto akce můžou vyžadovat oprávnění správce serveru nebo speciální znalosti o vašich místních serverech.

Brána usnadňuje rychlejší a bezpečnější komunikaci na pozadí. Tato komunikace komunikuje mezi uživatelem v cloudu, cloudovou službou brány a vaším místním zdrojem dat. Cloudová služba brány šifruje a ukládá vaše přihlašovací údaje ke zdroji dat a podrobnosti o bráně. Služba také směruje dotazy a jejich výsledky mezi uživatelem, bránou a vaším místním zdrojem dat.

Brána pracuje s branami firewall a používá jenom odchozí připojení. Veškerý provoz pochází z agenta brány jako zabezpečený odchozí provoz. Brána odesílá data z místních zdrojů do šifrovaných kanálů prostřednictvím [Azure Service Bus zasílání zpráv](../service-bus-messaging/service-bus-messaging-overview.md). Tato služba Service Bus vytváří kanál mezi bránou a volající službou, ale neukládá žádná data. Všechna data, která jsou přenášena přes bránu, jsou zašifrovaná.

![Architektura pro místní bránu dat](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> V závislosti na cloudové službě možná budete muset pro bránu nastavit zdroj dat.

Tyto kroky popisují, co se stane, když budete pracovat s prvkem, který je připojený k místnímu zdroji dat:

1. Cloudová služba vytvoří dotaz společně se zašifrovanými přihlašovacími údaji pro zdroj dat. Služba pak odešle dotaz a přihlašovací údaje do fronty brány ke zpracování.

1. Cloudová služba brány dotaz analyzuje a odešle požadavek pro Azure Service Bus zasílání zpráv.

1. Azure Service Bus zasílání zpráv odesílá nedokončené žádosti do brány.

1. Brána obdrží dotaz, dešifruje přihlašovací údaje a pomocí nich se připojí k jednomu nebo více zdrojům dat.

1. Brána odešle dotaz do zdroje dat pro spuštění.

1. Výsledky se odešlou ze zdroje dat zpátky do brány a potom do cloudové služby brány. Cloudová služba brány pak výsledky použije.

### <a name="authentication-to-on-premises-data-sources"></a>Ověřování pro místní zdroje dat

Uložené přihlašovací údaje se používají pro připojení z brány k místním zdrojům dat. Bez ohledu na uživatele brána použije k připojení uložené přihlašovací údaje. Existují výjimky ověřování pro konkrétní služby, jako je například DirectQuery a LiveConnect pro Analysis Services v Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Cloudové služby Microsoftu používají k ověřování uživatelů [službu Azure AD](../active-directory/fundamentals/active-directory-whatis.md) . Tenant Azure AD obsahuje uživatelská jména a skupiny zabezpečení. E-mailová adresa, kterou používáte pro přihlášení, je obvykle stejná jako hlavní název uživatele (UPN) pro váš účet.

### <a name="what-is-my-upn"></a>Co je můj hlavní název uživatele?

Pokud nejste správcem domény, možná neznáte hlavní název uživatele (UPN). Hlavní název uživatele pro svůj účet zjistíte spuštěním `whoami /upn` příkazu z pracovní stanice. I když výsledný výsledek vypadá jako e-mailová adresa, je výsledkem hlavní název uživatele (UPN) pro místní doménový účet.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synchronizace místní služby Active Directory s Azure AD

Hlavní název uživatele pro místní účty služby Active Directory a účty Azure AD musí být stejné. Ujistěte se, že každý místní účet Active Directory odpovídá vašemu účtu služby Azure AD. Cloudové služby znají jenom účty v rámci služby Azure AD. Nemusíte tedy přidávat účet do místní služby Active Directory. Pokud účet ve službě Azure AD neexistuje, nemůžete tento účet použít.

Tady jsou způsoby, jak můžete porovnat místní účty Active Directory se službou Azure AD.

* Přidejte účty do Azure AD ručně.

  Vytvořte účet v Azure Portal nebo v centru pro správu Microsoft 365. Ujistěte se, že název účtu odpovídá hlavnímu názvu uživatele (UPN) místního účtu služby Active Directory.

* Synchronizujte místní účty s vaším klientem služby Azure AD pomocí nástroje Azure Active Directory Connect.

  Nástroj Azure AD Connect poskytuje možnosti pro synchronizaci adresářů a nastavení ověřování. Mezi tyto možnosti patří synchronizace hodnot hash hesel, předávací ověřování a federace. Pokud nejste správcem tenanta nebo místní správce domény, požádejte správce IT, aby vám nastavil Azure AD Connect. Azure AD Connect zajistí, že hlavní název uživatele služby Azure AD odpovídá vašemu místnímu hlavnímu názvu uživatele služby Active Directory. Tato shoda pomáhá při použití Analysis Services živých připojení s možnostmi Power BI nebo jednotného přihlašování (SSO).

  > [!NOTE]
  > Synchronizace účtů pomocí nástroje Azure AD Connect vytvoří nové účty v tenantovi Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Nejčastější dotazy a řešení potíží

* [Místní brána dat – nejčastější dotazy](/data-integration/gateway/service-gateway-onprem-faq)
* [Řešení problémů s místní bránou dat](/data-integration/gateway/service-gateway-tshoot)
* [Monitorování a optimalizace výkonu brány](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Další kroky

* [Připojení k místním datům z Logic Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Funkce Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Konektory pro Azure Logic Apps](../connectors/apis-list.md)
