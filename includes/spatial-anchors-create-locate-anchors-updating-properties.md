## <a name="updating-properties-on-an-existing-cloud-spatial-anchor"></a>Aktualizují se vlastnosti na existující cloud prostorových ukotvení

Pokud chcete aktualizovat vlastnosti anchor, použijete metodu UpdateAnchorPropertiesAsync. Pokud dva nebo více zařízení. Zkuste aktualizovat vlastnosti pro stejné kotvě ve stejnou dobu, můžeme použít model optimistického řízení souběžnosti. To znamená, že prvním zápisu vyhraje.  Všechny ostatní operace zápisu se zobrazí chyba "Souběžnosti": aktualizace vlastností bylo by potřeba provést před dalším pokusem.
