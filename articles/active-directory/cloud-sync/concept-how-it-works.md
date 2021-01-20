---
title: Azure AD Connect podrobně Cloud Sync – jak to funguje
description: V tomto tématu najdete podrobné informace podrobně o tom, jak funguje cloudová synchronizace.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613416"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Podrobně Cloud Sync – jak to funguje

## <a name="overview-of-components"></a>Přehled komponent

![Jak to funguje](media/concept-how-it-works/how-1.png)

Synchronizace cloudu je postavená na službách Azure AD a má 2 klíčové komponenty:

- **Agent zřizování**: Agent zřizování cloudu Azure AD Connect je stejný Agent jako příchozí a založený na stejné technologii na straně serveru jako proxy aplikace a předávací ověřování. Vyžaduje a jenom odchozí připojení a agenti se automaticky aktualizují. 
- **Služba zřizování**: stejná služba zřizování jako odchozí zřizování a příchozí zřizování Workday, které používá model založený na plánovači. V případě synchronizace v cloudu se změny zřídí každých 2 minut.


## <a name="initial-setup"></a>Počáteční nastavení
Během počáteční instalace se provede několik věcí, které provedou cloudovou synchronizaci.  Jsou to: 

- **Během instalace agenta**: nakonfigurujete agenta pro domény služby AD, ze kterých chcete zřídit.  Tato konfigurace registruje domény ve službě hybridní identity a vytvoří odchozí připojení ke službě Service Bus, která naslouchá požadavkům.
- **Když povolíte zřizování**: vyberete doménu AD a povolíte zřizování, které se spouští každé 2 minuty. Volitelně můžete zrušit volbu synchronizace hodnot hash hesel a definovat e-mail s oznámením. Transformaci atributů můžete také spravovat pomocí Microsoft Graph rozhraní API.


## <a name="agent-installation"></a>Instalace agenta
Následuje návod k tomu, co se stane, když je agent zřizování cloudu nainstalovaný.

- Nejprve instalační program nainstaluje binární soubory agenta a službu agenta spuštěnou pod účtem virtuální služby (NETWORK SERVICE\AADProvisioningAgent).  Účet virtuální služby je speciální typ účtu, který nemá heslo a spravuje ho Windows.
- Instalační program potom spustí průvodce.
- Průvodce zobrazí výzvu k zadání přihlašovacích údajů Azure AD a potom ověří a načte token.
- Průvodce pak vyzve k zadání přihlašovacích údajů pro aktuálního počítače správce domény.
- Pomocí těchto přihlašovacích údajů je obecný účet spravované služby (GMSA) pro tuto doménu buď vytvořený, nebo umístěný a znovu použitý, pokud už existuje.
- Služba agenta je teď překonfigurovaná tak, aby běžela v GMSA.
- Průvodce nyní vyžádá o konfiguraci domény spolu s účtem správce podnikového (EA)/Domain Admin (DA) pro každou doménu, kterou má agent služby.
- Účet GMSA se pak aktualizuje pomocí oprávnění, která jim umožní přístup k jednotlivým doménám zadaným výše.
- V dalším kroku průvodce aktivuje registraci agenta.
- Agent vytvoří certifikát a použije token Azure AD, sám si zaregistruje a certifikát se službou pro registraci hybridní identity (služby).
- Průvodce aktivuje volání AgentResourceGrouping. Tímto voláním do příslušné služby správce je přiřazení agenta k jedné nebo více doménám služby AD ve své konfiguraci.
- Průvodce nyní restartuje službu agenta.
- Agent volá službu Bootstrap při restartování (a každých 10 minut následně), aby zkontrolovala aktualizace konfigurace.  Služba Bootstrap ověřuje identitu agenta.  Také aktualizuje čas posledního spuštění.  To je důležité, protože pokud se agenti nespustí, nezískávají aktualizované koncové body Service Bus a nemusí být schopni přijímat požadavky. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Co je systém pro správu identit mezi doménami (SCIM)?

[Specifikace SCIM](https://tools.ietf.org/html/draft-scim-core-schema-01) je standard, který slouží k automatizaci výměny informací o identitě uživatelů nebo skupin mezi doménami identity, jako je Azure AD. SCIM se stává jako de facto standard pro zřizování a při použití ve spojení s federačními standardy, jako je SAML nebo OpenID Connect, poskytuje správcům ucelené řešení založené na standardech pro správu přístupu.

Agent zřizování cloudu Azure AD Connect používá SCIM se službou Azure AD ke zřízení a zrušení zřízení uživatelů a skupin.

## <a name="synchronization-flow"></a>Průběh synchronizace
![zřizování ](media/concept-how-it-works/provisioning-4.png) po instalaci agenta a povolení zřizování dojde k následujícímu toku.

1.  Po nakonfigurování zavolá služba zřizování Azure AD hybridní službu Azure AD, aby přidala požadavek do služby Service Bus. Agent nepřetržitě udržuje odchozí připojení k Service Bus naslouchání požadavkům a okamžitě vybere pro požadavek na správu identit mezi doménami (SCIM). 
2.  Agent rozdělí požadavek na samostatné dotazy založené na typu objektu. 
3.  Služba AD vrátí výsledek agentovi a Agent bude tato data filtrovat před odesláním do služby Azure AD.  
4.  Agent vrátí odpověď SCIM do služby Azure AD.  Tyto odpovědi jsou založené na filtrování, ke kterému došlo v rámci agenta.  Agent používá k filtrování výsledků rozsah. 
5.  Služba zřizování zapisuje změny do služby Azure AD.
6. Pokud se jedná o rozdílovou synchronizaci na rozdíl od úplné synchronizace, použije se soubor cookie nebo vodoznak. Nové dotazy dostanou změny z tohoto souboru cookie/vodoznaku i dál.

## <a name="supported-scenarios"></a>Podporované scénáře:
Pro synchronizaci cloudu se podporují následující scénáře.


- **Stávající Hybrid Customer s novou doménovou** strukturou: Azure AD Connect synchronizace se používá pro primární doménové struktury. Synchronizace cloudu se používá ke zřizování z doménové struktury služby AD (včetně odpojených). Další informace najdete v [tomto](tutorial-existing-forest.md)kurzu.

 ![Existující Hybrid](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Nový hybridní zákazník**: Azure AD Connect synchronizace se nepoužívá. Synchronizace cloudu se používá ke zřizování z doménové struktury AD.  Další informace najdete v [tomto](tutorial-single-forest.md)kurzu.
 
 ![Noví zákazníci](media/tutorial-single-forest/diagram-2.png)

- **Stávající Hybrid Customer**: Azure AD Connect synchronizace se používá pro primární doménové struktury. Cloud Synchronization se nasazuje pro malou skupinu uživatelů v primárních [doménových](tutorial-existing-forest.md)strukturách.

 ![Existující pilotní nasazení](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Další informace najdete v tématu [podporované topologie](plan-cloud-sync-topologies.md).



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
