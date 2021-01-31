---
title: Principy připojení k síti virtuálních počítačů s Windows
titleSuffix: Azure
description: Informace o připojení k síti virtuálních počítačů s Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: e4149864e16196b695d38a8c46ab5af835453412
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221206"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Principy připojení k síti virtuálních počítačů s Windows

Virtuální počítač s Windows nabízí možnost hostování klientských relací na hostitelích relací, kteří běží na Azure. Společnost Microsoft spravuje části služeb jménem zákazníka a poskytuje zabezpečené koncové body pro připojení klientů a hostitelů relací. Následující diagram obsahuje podrobný přehled síťových připojení používaných virtuálními počítači Windows.

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagram síťových připojení k virtuálním plochám Windows" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Připojení k relaci

Virtuální plocha Windows používá protokol RDP (Remote Desktop Protocol) (RDP) k zajištění funkcí vzdáleného zobrazení a vstupu přes síťová připojení. Protokol RDP byl původně vydaný s verzí Windows NT 4,0 Terminal Server Edition a neustále se vyvíjí s každou verzí Microsoft Windows a Windows serveru. Od začátku se protokol RDP vyvíjel nezávisle na jeho základním přenosovém zásobníku a dnes podporuje více typů přenosů.

## <a name="reverse-connect-transport"></a>Zpětně propojit přenos

Virtuální počítač s Windows používá pro vytvoření vzdálené relace a pro přenos provozu protokolu RDP přenos zpětného připojení. Na rozdíl od místních nasazení vzdálené plochy nepoužívá přenos opačného připojení naslouchací proces TCP pro příjem příchozích připojení RDP. Místo toho používá odchozí připojení k infrastruktuře virtuálních počítačů s Windows prostřednictvím připojení HTTPS.

## <a name="session-host-communication-channel"></a>Komunikační kanál hostitele relace

Po spuštění hostitele relace virtuálních klientských počítačů se systémem Windows vytvoří služba zavaděče agenta vzdálené plochy trvalý komunikační kanál zprostředkovatele virtuálních klientských počítačů. Tento komunikační kanál je vrstvený nad připojením protokolu TLS (Secure Transport Layer Security) a slouží jako sběrnice pro výměnu zpráv služby mezi hostitelem relace a infrastrukturou virtuální plochy Windows.

## <a name="client-connection-sequence"></a>Pořadí připojení klienta

Pořadí připojení klienta popsané níže:

1. Pomocí podporovaného uživatele klienta virtuální plochy Windows se přihlásí k odběru pracovního prostoru virtuálních počítačů s Windows.
2. Azure Active Directory ověří uživatele a vrátí token použitý k zobrazení výčtu prostředků, které jsou k dispozici pro uživatele.
3. Klient předává token službě předplatného informačního kanálu virtuálního počítače Windows.
4. Služba odběru informačního kanálu virtuálních počítačů s Windows ověřuje token.
5. Služba předplatného informačního kanálu virtuálních počítačů systému Windows předává seznam dostupných ploch a vzdálených aplikací RemoteApp zpátky klientovi ve formě konfigurace s nepodepsaným připojením.
6. Klient ukládá konfiguraci připojení pro všechny dostupné prostředky v sadě souborů RDP.
7. Když uživatel vybere prostředek k připojení, klient použije přidružený soubor. RDP a naváže zabezpečené připojení TLS 1,2 k nejbližší instanci brány virtuálního počítače s Windows a předá informace o připojení.
8. Brána virtuální plochy Windows ověří požadavek a požádá zprostředkovatele virtuálních počítačů s Windows o orchestraci připojení.
9. Zprostředkovatel virtuálních počítačů s Windows identifikuje hostitele relace a používá dříve vytvořený trvalý komunikační kanál k inicializaci připojení.
10. Zásobník vzdálené plochy inicializuje připojení TLS 1,2 ke stejné instanci brány virtuálního počítače se systémem Windows, jak ji používá klient nástroje.
11. Po připojení klienta i hostitele relace k bráně zahájí brána předávání nezpracovaných dat mezi oběma koncovými body, což vytvoří základní přenos zpětného připojení pro protokol RDP.
12. Po nastavení základního přenosu spustí klient protokol RDP handshake.

## <a name="connection-security"></a>Zabezpečení připojení

TLS 1,2 se používá pro všechna připojení inicializovaná z klientů a hostitelů relací k součástem infrastruktury virtuálních počítačů s Windows. Virtuální počítač s Windows používá stejné šifry TLS 1,2 jako [přední dveře Azure](../frontdoor/front-door-faq.md#what-are-the-current-cipher-suites-supported-by-azure-front-door). Je důležité se ujistit, že klientské počítače i hostitelé relací můžou tyto šifry použít.
Pro přenos zpětného připojení se klient a hostitel relace připojí k bráně virtuální plochy Windows. Po navázání připojení TCP klient nebo hostitel relace ověří certifikát brány virtuální plochy Windows.
Po zřízení základního přenosu protokol RDP vytvoří vnořené připojení TLS mezi klientem a hostitelem relace pomocí certifikátů hostitele relace. Ve výchozím nastavení se certifikát používaný pro šifrování RDP během nasazení vygeneruje v operačním systému sami. V případě potřeby můžou zákazníci nasazovat centrálně spravované certifikáty vydané certifikační autoritou organizace. Další informace o konfiguraci certifikátů najdete v [dokumentaci k Windows serveru](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Další kroky

* Další informace o požadavcích na šířku pásma pro virtuální počítač s Windows najdete v článku [Principy požadavků na šířku pásma protokol RDP (Remote Desktop Protocol) (RDP) pro virtuální počítač s Windows](rdp-bandwidth.md).
* Pokud chcete začít se službou QoS (Quality of Service) pro virtuální počítače s Windows, přečtěte si téma [implementace technologie QoS (Quality of Service) pro virtuální počítače s Windows](rdp-quality-of-service-qos.md).
