---
title: Poradce při potížích s odchozím připojením SMTP v Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problémy s odchozím připojením SMTP v Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: b8acb50978c5932fe6e6838be86b65c12a0984ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058938"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Řešení problémů s odchozím připojením SMTP v Azure

listopadu 2017 jsou odchozí e-mailové zprávy odesílané přímo do externích domén (například outlook.com a gmail.com) z virtuálního počítače (VM) dostupné jenom pro určité typy předplatného v Microsoft Azure. Odchozí připojení SMTP, která používají port TCP 25, byla blokována. (Port 25 se používá především pro neověřené doručování e-mailů.)

Tato změna chování platí pouze pro nová předplatná a nová nasazení od listopadu 15, 2017.

## <a name="recommended-method-of-sending-email"></a>Doporučený způsob odesílání e-mailů
Doporučujeme používat ověřené služby přenosu SMTP (které se obvykle připojují prostřednictvím portu TCP 587 nebo 443, ale podporují i další porty) k odesílání e-mailů z virtuálních počítačů Azure nebo z Azure App Services. Tyto služby se používají k udržení reputace IP nebo domény, aby se minimalizovala možnost, že poskytovatelé e-mailu třetích stran odmítnou zprávu. Tyto služby přenosu SMTP zahrnují, ale nejsou omezeny na [SendGrid](https://sendgrid.com/partners/azure/). Je také možné, že máte zabezpečenou službu přenosu SMTP, která běží místně, kterou můžete použít.

Používání těchto služeb doručování e-mailů není v Azure omezeno, bez ohledu na typ předplatného.

## <a name="enterprise-agreement"></a>Smlouva Enterprise
Pro uživatele Enterprise Agreement Azure nedojde k žádné změně v technické schopnosti odesílat e-maily bez použití ověřeného přenosu. Noví i stávající uživatelé smlouvy Enterprise si můžou vyzkoušet doručování odchozích e-mailů z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu bez jakýchkoli omezení z platformy Azure. I když není zaručeno, že poskytovatelé e-mailu budou přijímat příchozí e-maily od daného uživatele, pokusy o doručení nebudou blokovány platformou Azure pro virtuální počítače v rámci předplatných enterprise smlouvy. Budete muset spolupracovat přímo s poskytovateli e-mailu, abyste opravili problémy s doručováním zpráv nebo filtrováním nevyžádané pošty, které se týkají konkrétních poskytovatelů.

## <a name="pay-as-you-go"></a>Průběžné platby
Pokud jste se zaregistrovali před 15. Budete si moct dál vyzkoušet doručování odchozích e-mailů z virtuálních aplikací Azure v rámci těchto předplatných přímo externím poskytovatelům e-mailu bez jakýchkoli omezení z platformy Azure. Opět platí, že není zaručeno, že poskytovatelé e-mailu budou přijímat příchozí e-maily od daného uživatele a uživatelé budou muset pracovat přímo s poskytovateli e-mailu, aby opravili jakékoli problémy s doručováním zpráv nebo filtrováním spamu, které se týkají konkrétních poskytovatelů.

Pro předplatná s průběžným platbou nebo microsoft partnerskou síť, která byla vytvořena po 15. Pokud chcete možnost odesílat e-maily z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu (nepoužíváte ověřené přenosové relace SMTP), můžete požádat o odebrání omezení. Žádosti budou přezkoumány a schváleny podle uvážení společnosti Microsoft a budou uděleny až po dalších kontrolách proti podvodům. Chcete-li podat žádost, otevřete případ podpory pomocí následujícího typu problému: **Připojení technické** > **virtuální sítě** > **Connectivity** > **nelze odeslat e-mail (SMTP/Port 25)**. Ujistěte se, že přidáte podrobnosti o tom, proč vaše nasazení musí odesílat poštu přímo poskytovatelům pošty namísto použití ověřeného přenosu.

Po pay-as-you-go nebo Microsoft Partner Network předplatné je osvobozena a virtuální počítače byly "Zastaveno" & "Spuštěno" z portálu Azure, všechny virtuální počítače v rámci tohoto předplatného budou osvobozeny do budoucna. Výjimka se vztahuje pouze na požadované předplatné a vztahuje se pouze na provoz virtuálních strojů směrovaný přímo na internet. Směrování portu 25 provoz prostřednictvím služeb Azure PaaS, jako je [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) není podporována.

> [!NOTE]
> Společnost Microsoft si vyhrazuje právo tuto výjimku odvolat, pokud je zjištěno, že došlo k porušení smluvních podmínek.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure v otevřeném, vzdělávacím, bizsparku a bezplatné zkušební verzi
Pokud jste po 15. přímo poskytovatelům e-mailu. Omezení se provádějí, aby se zabránilo zneužívání. Nebudou uděleny žádné žádosti o odstranění tohoto omezení.

Pokud používáte tyto typy předplatného, doporučujeme použít služby přenosu SMTP, jak je uvedeno výše v tomto článku nebo změnit typ předplatného.

## <a name="cloud-service-provider-csp"></a>Poskytovatel cloudových služeb (CSP)

Pokud používáte prostředky Azure prostřednictvím csp, můžete požádat o csp vytvořit žádost o odblokování výjimky se společností Microsoft vaším jménem, pokud nelze použít zabezpečené přenossmTP.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste problém rychle vyřešili pomocí následujícího typu problému: Typ problému **správy předplatného:** **Žádost o povolení toku e-mailů portu 25**.
