<script>
    import '../assets/styles.css';
    import ribbon from '../assets/ribbon-chart.svg'
</script>

<div class="graph-title">
    Toronto Land Cover Transitions (1939-2017)
</div>

<div id="legend-ribbon">
    <svg width="430px" height="25px">
        <rect x="360" y="0" width="70" height="25" fill="#FFFFFF" stroke="#E1E1E1" stroke-width="1"/>
        <rect x="288" y="0" width="70" height="25" fill="#D0D1C9" stroke="#FFFFFF" stroke-width="1"/>
        <rect x="216" y="0" width="70" height="25" fill="#6FC7EA" stroke="#FFFFFF" stroke-width="1"/>
        <rect x="144" y="0" width="70" height="25" fill="#0D534D" stroke="#FFFFFF" stroke-width="1"/>
        <rect x="72" y="0" width="70" height="25" fill="#00A189" stroke="#FFFFFF" stroke-width="1"/>
        <rect x="0" y="0" width="70" height="25" fill="#f1c500" stroke="#FFFFFF" stroke-width="1"/>
        <text id="legend-label" x="22" y="17">Built</text>
        <text id="legend-label" x="92" y="17">Grass</text>
        <text id="legend-label-dark"x="162" y="17">Forest</text>
        <text id="legend-label" x="234" y="17">Water</text>
        <text id="legend-label" x="310" y="17">Bare</text>
        <text id="legend-label" x="372" y="17">No Data</text>
    </svg>
</div>

<div class=chart-margin>
    <img src={ribbon} alt="Ribbon chart showing changes of land cover from 1939 to 2017">
</div>


<style>
.chart-margin{
    margin: 0 auto;
    margin-top: 10px;
    margin-bottom: 10px;
    width: 567px;
    height: 302px;
    padding-left: 0px;
    padding-right: 0px;
}

.graph-title {
    text-align: center;
    font-size: 30px;
    font-family: 'TradeGothicBold', sans-serif;
    font-weight: normal;
    height: 25px;
    font-size: 20px;
    color: var(--brandDarkBlue);
    background-color: var(--brandWhite);
    max-width: 600px;
    margin: 0 auto;
    padding-left: 15px;
    padding-right: 15px;
    margin-bottom: 0px;
    position: relative;
}

#legend-ribbon {
        margin: 0 auto;
        margin-top: 10px;
        margin-bottom: 10px;
        width: 430px;
        height: 30px;
        padding: 0px;
    }

#legend-label {
        font-family: 'Roboto', sans-serif;
        font-weight: normal;
        font-size: 12px;
        fill: var(--brandBlack);
}

#legend-label-dark {
        font-family: 'Roboto', sans-serif;
        font-weight: normal;
        font-size: 12px;
        fill: var(--brandWhite);
}

</style>