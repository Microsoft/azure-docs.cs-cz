---
title: Režim služby ve službě Azure Signal
description: Přehled různých režimů služby ve službě Azure Signal Service – vysvětlení jejich rozdílů a příslušných scénářů uživatelů
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 2cdce64b0ff03521a5848e2b4fd6e01431b5cc16
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514810"
---
# <a name="service-mode-in-azure-signalr-service"></a>Režim služby ve službě Azure Signal

Režim služby je důležitý koncept ve službě Azure Signal. Při vytváření nového prostředku signalizace budete požádáni o zadání režimu služby:

:::image type="content" source="media/concept-service-mode/create.png" alt-text="Zvolit režim služby při vytvoření":::

Můžete ho také později změnit v nabídce nastavení:

:::image type="content" source="media/concept-service-mode/update.png" alt-text="Aktualizovat režim služby":::

Služba signalizace Azure v současné době podporuje tři režimy služby: **výchozí**, bez **serveru** a **klasický**. Prostředek signalizace se bude chovat odlišně v různých režimech. V tomto článku se dozvíte o rozdílech a o tom, jak zvolit správný režim služby na základě vašeho scénáře.

## <a name="default-mode"></a>Výchozí režim

Výchozím režimem je výchozí hodnota pro režim služby při vytváření nového prostředku signalizace. V tomto režimu funguje vaše aplikace jako typická aplikace pro signalizaci ASP.NET Core (nebo ASP.NET), kde máte webový server, který je hostitelem rozbočovače (označovaného jako hub Server), a klienti mohou mít oboustrannou komunikaci v reálném čase se serverem hub. Jediným rozdílem je, že místo přímého připojení klienta a serveru se klient a Server připojují ke službě Signal Service a službu používají jako proxy. Níže je diagram, který ilustruje typickou strukturu aplikace ve výchozím režimu:

:::image type="content" source="media/concept-service-mode/default.png" alt-text="Struktura aplikace ve výchozím režimu":::

Takže pokud máte aplikaci pro signalizaci a chcete ji integrovat se službou Signaler, výchozí režim by měl být ve většině případů správným výběrem.

### <a name="connection-routing-in-default-mode"></a>Směrování připojení ve výchozím režimu

Ve výchozím režimu budou připojení protokolu WebSocket mezi serverem centra a službou Signal (označovaným jako připojení serveru). Tato připojení se používají k přenosu zpráv mezi serverem a klientem. Když je připojen nový klient, služba signalizace nasměruje klienta na jeden hub Server (Předpokládejme, že máte více než jeden server) prostřednictvím stávajících připojení k serveru. Pak připojení klienta během své životnosti vytvoří stejný hub Server. Když klient odesílá zprávy, vždy přejde na stejný hub Server. S tímto chováním můžete bezpečně udržovat některé stavy pro jednotlivá připojení na serveru rozbočovače. Pokud například chcete streamovat mezi serverem a klientem, nemusíte brát v úvahu případ, že datové pakety přecházejí na různé servery.

> [!IMPORTANT]
> To také znamená, že se klient výchozího režimu nemůže připojit, aniž by se server připojoval jako první. Pokud jsou všechny servery rozbočovačů odpojené z důvodu přerušení sítě nebo restartování serveru, zobrazí se při připojení klienta chyba informující o tom, že server není připojený. Proto je vaše zodpovědnost za to, že je k dispozici alespoň jeden hub server připojený ke službě signalizace (například mít několik rozbočovačů a ujistěte se, že nepřejde do režimu offline ve stejnou dobu jako Údržba).

Tento model směrování také znamená, že centrální server přejde do režimu offline. připojení, která jsou směrována na tento server, budou zahozena. Proto byste měli očekávat připojení, když je server rozbočovače offline kvůli údržbě a správné opětovné připojení, takže nebude mít negativní vliv na vaši aplikaci.

## <a name="serverless-mode"></a>Režim bez serveru

Režim bez serveru, protože tento název implikuje, je režim, který nemůže mít žádný hub Server. V porovnání s výchozím režimem není v klientovi tohoto režimu připojení k serveru rozbočovače. Všechna připojení jsou připojená ke službě v režimu bez serveru a služba zodpovídá za údržbu připojení klientů, jako je zpracování příkazů pro ověření klienta (ve výchozím režimu se jedná o servery hub).

V tomto režimu není také žádné připojení k serveru (Pokud se pokusíte vytvořit připojení k serveru pomocí sady SDK služby, zobrazí se chyba). Proto není k dispozici ani směrování připojení a vytrvalost serveru klienta (jak je popsáno v části výchozí režim). I když ale můžete mít serverovou aplikaci k odesílání zpráv klientům. To můžete provést dvěma způsoby, používat [rozhraní REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) pro jednorázové odesílání nebo prostřednictvím připojení protokolu WebSocket, abyste mohli rychleji odesílat více zpráv (Všimněte si, že toto připojení typu WebSocket se liší od připojení k serveru).

> [!NOTE]
> V [sadě SDK pro správu](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)služby signaler jsou podporované jak REST API, tak i WebSocket. Pokud používáte jiný jazyk než .NET, můžete také ručně vyvolat rozhraní REST API, která následují tato [specifikace](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md).
>
> Pokud používáte Azure Functions, můžete použít [vazby služby signalizace pro Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service) (dále označované jako vazby funkce) a odesílat zprávy jako výstupní vazbu.

Je taky možné, aby serverová aplikace přijímala zprávy a události připojení od klientů. Služba bude doručovat zprávy a události připojení k předkonfigurovaným koncovým bodům (nazývaným nadřazeným) pomocí webhooků. Porovnání s výchozím režimem není nijak zaručeno, že vytrvalost a požadavky HTTP můžou být méně efektivní než připojení pomocí protokolu WebSocket.

Další informace o tom, jak nakonfigurovat nadřazený datový proud, najdete v tomto [dokumentu](https://docs.microsoft.com/azure/azure-signalr/concept-upstream).

Níže je uvedený diagram, který znázorňuje, jak funguje režim bez serveru:

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="Struktura aplikace v režimu bez serveru":::

> [!NOTE]
> Všimněte si prosím, že ve výchozím režimu můžete použít také REST API/Management SDK/funkce vazby k přímému odesílání zpráv klientovi, pokud nechcete projít serverem centrálního serveru. Ale ve výchozím režimu se připojení klienta pořád zpracovávají servery hub a v tomto režimu nebudou fungovat.

## <a name="classic-mode"></a>Klasický režim

Klasický je smíšený režim s výchozím režimem a režim bez serveru. V tomto režimu se v režimu připojení rozhodnete, jestli se k navázání připojení klienta připojí hub Server. Pokud je server rozbočovače, bude připojení klienta směrováno na server rozbočovače. V opačném případě zadáte režim bez serveru, ve kterém nebude možné doručit zprávu klienta na server do serveru hub. Tato akce způsobí některé nesrovnalosti, například pokud jsou všechny servery rozbočovačů po krátkou dobu nedostupné, všechna připojení klientů vytvořená během této doby budou v režimu bez serveru a nemohou odesílat zprávy do serveru hub.

> [!NOTE]
> Klasický režim je hlavně kvůli zpětné kompatibilitě pro tyto aplikace vytvořené před výchozím režimem a režim bez serveru. Tento režim se už důrazně nedoporučuje používat. Pro nové aplikace prosím zvolte výchozí nastavení nebo bez serveru v závislosti na vašem scénáři. U existujících aplikací se také doporučuje zkontrolovat případy použití a zvolit správný režim služby.

Klasický režim také nepodporuje některé nové funkce, jako je například nadřazený v režimu bez serveru.

## <a name="choose-the-right-service-mode"></a>Volba správného režimu služby

Nyní byste měli porozumět rozdílům mezi režimy služby a vědět, jak mezi nimi vybírat. Jak už jste se naučili v předchozí části, není klasický režim podporován a měli byste volit jenom mezi výchozími a bez serveru. Tady je několik dalších tipů, které vám pomůžou vytvořit si správnou volbu pro nové aplikace a vyřadit klasický režim pro stávající aplikace.

* Pokud jste již obeznámeni s tím, jak knihovna signálů funguje a chcete přejít z místního opakovače na používání služby signalizace Azure, vyberte možnost výchozí režim. Výchozí režim funguje přesně stejným způsobem jako signál pro místní hostování (a můžete použít stejný programovací model v knihovně Signal), služba signalizace funguje jenom jako proxy mezi klienty a servery rozbočovače.

* Pokud vytváříte novou aplikaci a nechcete spravovat připojení k serveru a serveru centrálního serveru, vyberte režim bez serveru. Tento režim obvykle spolupracuje společně s Azure Functions, takže nemusíte mít vůbec žádný server. Stále můžete mít oboustrannou komunikaci (pomocí REST API/Management SDK/vazby funkce + nadřazený), ale programovací model se liší od knihovny signalizace.

* Pokud máte oba servery centrálního centra pro poskytování připojení klienta a back-end aplikace pro přímé odesílání zpráv klientům (například prostřednictvím REST API), měli byste zvolit výchozí režim. Mějte na paměti, že klíčový rozdíl mezi výchozím režimem a režim bez serveru je bez ohledu na to, zda máte servery rozbočovače a jak jsou směrována připojení klientů. REST API/Management SDK/vazby funkce lze použít v obou režimech.

* Pokud máte ve skutečnosti smíšený scénář, například máte dvě různá centra na stejný prostředek, který se používá jako tradiční centrum signalizace a druhý, který se používá s Azure Functions a nemá hub Server, měli byste je opravdu oddělit do dvou prostředků signalizace ve výchozím režimu a v režimu bez serveru.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat výchozí režim a režim bez serveru, najdete v následujících článcích:

* [Interní informace služby Azure SignalR Service](signalr-concept-internals.md)

* [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](signalr-concept-serverless-development-config.md)
