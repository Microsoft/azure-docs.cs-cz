---
title: Konfigurace sítě VPN site-to-site (S2S) pro použití se soubory Azure | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat síť VPN site-to-site (S2S) pro použití se soubory Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ae3d38d92990d7a1af4146c25b017286ebd29352
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061036"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurace sítě VPN site-to-site pro použití se soubory Azure
Připojení VPN site-to-site (S2S) můžete použít k připojení sdílených složek Azure přes SMB z místní sítě, aniž byste otevřeli port 445. Vpn site-to-site můžete nastavit pomocí [azure vpn gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), což je prostředek Azure nabízející služby VPN a nasazený ve skupině prostředků vedle účtů úložiště nebo jiných prostředků Azure.

![Graf topologie ilustrující topologii brány Azure VPN spojující sdílenou složku Azure s místním webem pomocí Sítě VPN S2S](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Důrazně doporučujeme, abyste si přečetli [přehled sítí Azure Files,](storage-files-networking-overview.md) než budete pokračovat v tomto článku pro úplnou diskusi o možnostech sítě, které jsou k dispozici pro soubory Azure.

Článek podrobně popisuje kroky konfigurace sítě VPN site-to-Site pro připojení sdílených složek Azure přímo místně. Pokud chcete směrovat synchronizační provoz pro Azure File Sync přes síť VPN site-to-site, přečtěte si prosím [konfiguraci nastavení proxy serveru Azure File Sync a brány firewall](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Požadavky
- Sdílená složka Azure, kterou chcete připojit místně. Sdílené složky Azure se nasazují v rámci účtů úložiště, což jsou konstrukce správy, které představují sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou kontejnery objektů blob nebo fronty. Další informace o nasazení sdílených složek a úložiště Azure najdete v [části Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).

- Soukromý koncový bod pro účet úložiště obsahující sdílenou složku Azure, kterou chcete připojit místně. Další informace o tom, jak vytvořit privátní koncový bod, najdete [v tématu Konfigurace síťových koncových bodů Souborů Azure](storage-files-networking-endpoints.md?tabs=azure-portal). 

- Síťové zařízení nebo server v místním datovém centru, který je kompatibilní s Azure VPN Gateway. Soubory Azure je agnostik místní síťové zařízení vybrané, ale Azure VPN Gateway udržuje [seznam testovaných zařízení](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Různá síťová zařízení nabízejí různé funkce, vlastnosti výkonu a funkce správy, proto je při výběru síťového zařízení zvažte.

    Pokud nemáte existující síťové zařízení, systém Windows Server obsahuje integrovanou roli serveru, směrování a vzdálený přístup (RRAS), která může být použita jako místní síťové zařízení. Další informace o konfiguraci směrování a vzdáleného přístupu v systému Windows Server naleznete v [tématu BRÁNA RAS](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Přidání účtu úložiště do virtuální sítě
Na webu Azure Portal přejděte na účet úložiště obsahující sdílenou složku Azure, kterou chcete připojit místně. V obsahu účtu úložiště vyberte položku **Brány firewall a virtuální sítě.** Pokud jste při vytváření účtu úložiště nepřidali virtuální síť, výsledné podokno by mělo mít vybrané přepínač **povolit přístup z** **přepínacích sítí** pro všechny sítě.

Pokud chcete přidat účet úložiště do požadované virtuální sítě, vyberte **Vybrané sítě**. V podnadpisu **Virtuální sítě** klikněte na položku + Přidat existující **virtuální síť** nebo **+Přidat novou virtuální síť** v závislosti na požadovaném stavu. Vytvoření nové virtuální sítě bude mít za následek vytvoření nového prostředku Azure. Nový nebo existující prostředek virtuální sítě nemusí být ve stejné skupině prostředků nebo předplatné jako účet úložiště, ale musí být ve stejné oblasti jako účet úložiště a skupiny prostředků a předplatného, do kterých nasadíte virtuální síť, musí odpovídat té, do které budete mít nasaďte bránu VPN. 

![Snímek obrazovky s portálem Azure, který dává možnost přidat existující nebo novou virtuální síť do účtu úložiště](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Pokud přidáte existující virtuální síť, budete vyzváni k výběru jedné nebo více podsítí této virtuální sítě, do které by měl být účet úložiště přidán. Pokud vyberete novou virtuální síť, vytvoříte podsíť jako součást vytvoření virtuální sítě a můžete přidat další později prostřednictvím výsledného prostředku Azure pro virtuální síť.

Pokud jste k předplatnému ještě nepřidali účet úložiště, bude nutné přidat koncový bod služby Microsoft.Storage do virtuální sítě. To může nějakou dobu trvat a dokud nebude tato operace dokončena, nebudete mít přístup ke sdíleným položkám souborů Azure v rámci tohoto účtu úložiště, včetně připojení VPN. 

## <a name="deploy-an-azure-vpn-gateway"></a>Nasazení brány Azure VPN
V obsahu portálu Azure vyberte **Vytvořit nový prostředek** a vyhledejte *bránu virtuální sítě*. Brána virtuální sítě musí být ve stejném předplatném, oblasti Azure a skupině prostředků jako virtuální síť, kterou jste nasadili v předchozím kroku (všimněte si, že skupina prostředků se automaticky vybere při výběru virtuální sítě). 

Pro účely nasazení brány Azure VPN je nutné naplnit následující pole:

- **Název**: Název prostředku Azure pro bránu VPN. Tento název může být libovolný název, který považujete za užitečný pro vaši správu.
- **Oblast**: Oblast, do které bude nasazena brána VPN.
- **Typ brány**: Pro účely nasazení sítě VPN typu Site-to-Site je nutné vybrat **možnost VPN**.
- **Typ VPN**: V závislosti na zařízení VPN můžete zvolit buď *route*,* nebo **zásady.** Virtuální ny založené na směrování podporují IKEv2, zatímco virtuální ny založené na zásadách podporují pouze IKEv1. Další informace o dvou typech bran VPN najdete v tématu [O branách VPN založených na zásadách a na směrování.](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **Skladová položka**: Skladová položka řídí počet povolených tunelů site-to-site a požadovaný výkon sítě VPN. Chcete-li vybrat příslušnou skladovou položku pro případ použití, podívejte se do výpisu [skladové položky brány.](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) Skladová položka brány VPN může být v případě potřeby později změněna.
- **Virtuální síť**: Virtuální síť, kterou jste vytvořili v předchozím kroku.
- **Veřejná IP adresa**: IP adresa VPN Gateway, která bude vystavena internetu. Pravděpodobně budete muset vytvořit novou ADRESU IP, ale můžete také použít existující nepoužitou adresu IP, pokud je to vhodné. Pokud vyberete **možnost Vytvořit nový**, bude ve stejné skupině prostředků jako brána VPN vytvořená nová IP adresa a název adresy Veřejné **IP** adresy bude název nově vytvořené IP adresy. Pokud vyberete **Použít existující**, musíte vybrat existující nepoužitou adresu IP.
- **Povolit aktivní aktivní režim**: Pouze **vyberte Povoleno,** pokud vytváříte konfiguraci brány aktivní a aktivní, jinak ponechte **možnost Zakázáno.** Další informace o aktivním aktivním režimu najdete v tématu [Vysoce dostupné připojení mezi místními a virtuální mise](../../vpn-gateway/vpn-gateway-highlyavailable.md)k virtuální síti .
- **Konfigurace protokolu ASN protokolu BGP**: Možnost **Povoleno** vyberte pouze v případě, že konfigurace toto nastavení vyžaduje konkrétně. Další informace o tomto nastavení najdete [v tématu O protokolu BGP pomocí služby Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Chcete-li vytvořit bránu VPN, vyberte **Zkontrolovat + vytvořit.** Úplné vytvoření a nasazení brány VPN může trvat až 45 minut.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Vytvoření brány místní sítě pro místní bránu 
Brána místní sítě je prostředek Azure, který představuje vaše místní síťové zařízení. V obsahu portálu Azure vyberte **Vytvořit nový prostředek** a vyhledejte *bránu místní sítě*. Brána místní sítě je prostředek Azure, který se nasadí vedle vašeho účtu úložiště, virtuální sítě a brány VPN, ale nemusí být ve stejné skupině prostředků nebo předplatnéjako účet úložiště. 

Pro účely nasazení prostředku brány místní sítě je nutné naplnit následující pole:

- **Název**: Název prostředku Azure pro bránu místní sítě. Tento název může být libovolný název, který považujete za užitečný pro vaši správu.
- **IP adresa**: Veřejná IP adresa místní brány v místním prostředí.
- **Adresní prostor**: Rozsahy adres pro síť, kterou tato brána místní sítě představuje. Můžete přidat více rozsahů adresního prostoru, ale ujistěte se, že rozsahy, které zde zadáte, se nepřekrývají s rozsahy jiných sítí, ke kterým se chcete připojit. 
- **Konfigurace nastavení protokolu BGP**: Nastavení protokolu BGP nakonfigurujte pouze v případě, že konfigurace toto nastavení vyžaduje. Další informace o tomto nastavení najdete [v tématu O protokolu BGP pomocí služby Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Předplatné**: Požadované předplatné. To nemusí odpovídat předplatné používané pro bránu VPN nebo účet úložiště.
- **Skupina prostředků**: Požadovaná skupina prostředků. To nemusí odpovídat skupině prostředků používané pro bránu VPN nebo účet úložiště.
- **Umístění**: Oblast Azure, ve které by měl být vytvořen prostředek místní síťové brány. To by se mělo shodovat s oblastí, kterou jste vybrali pro bránu VPN a účet úložiště.

Vyberte **Vytvořit,** chcete-li vytvořit prostředek brány místní sítě.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurace místního síťového zařízení
Konkrétní kroky konfigurace místního síťového zařízení závisí na síťovém zařízení, které vaše organizace vybrala. V závislosti na zařízení, které vaše organizace zvolila, může mít [seznam testovaných zařízení](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) odkaz na pokyny dodavatele zařízení pro konfiguraci pomocí služby Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>Vytvoření připojení Site-to-Site
Chcete-li dokončit nasazení sítě VPN S2S, musíte vytvořit připojení mezi místním síťovým zařízením (reprezentovaném prostředkem brány místní sítě) a bránou VPN. Chcete-li to provést, přejděte na bránu VPN, kterou jste vytvořili výše. V obsahu brány VPN vyberte **Připojení**a klepněte na tlačítko **Přidat**. Výsledné podokno **Přidat připojení** vyžaduje následující pole:

- **Název**: Název připojení. Brána VPN může hostovat více připojení, takže vyberte název užitečný pro vaši správu, který bude odlišit toto konkrétní připojení.
- **Typ připojení**: Vzhledem k tomu, že se jedná o připojení S2S, vyberte v rozevíracím seznamu **lokalitu k webu (IPSec).**
- **Brána virtuální sítě**: Toto pole je automaticky vybráno pro bránu VPN, ke které vytváříte připojení, a nelze je změnit.
- **Brána místní sítě**: Toto je brána místní sítě, kterou chcete připojit k bráně VPN. Výsledné podokno výběru by mělo mít název brány místní sítě, kterou jste vytvořili výše.
- **Sdílený klíč (PSK):** Směs písmen a čísel, která se používá k vytvoření šifrování pro připojení. Stejný sdílený klíč musí být použit v bráně virtuální sítě i místní sítě. Pokud zařízení brány neposkytuje, můžete si ho vytvořit tady a poskytnout ho svému zařízení.

Chcete-li vytvořit připojení, vyberte **ok.** Prostřednictvím stránky **Připojení** můžete ověřit, zda bylo připojení úspěšně provedeno.

## <a name="mount-azure-file-share"></a>Připojení sdílené složky Azure 
Posledním krokem při konfiguraci S2S VPN je ověření, že funguje pro soubory Azure. Můžete to provést připojením sdílené složky Azure místně s preferovaným os. Podívejte se na pokyny k montáži oS zde:

- [Windows](storage-how-to-use-files-windows.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Viz také
- [Přehled sítí Azure Files](storage-files-networking-overview.md)
- [Konfigurace sítě VPN point-to-site (P2S) v systému Windows pro použití se soubory Azure](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurace vpn point-to-site (P2S) na Linuxu pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md)