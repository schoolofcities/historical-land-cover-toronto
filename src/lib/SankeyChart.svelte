<script>
    import '../assets/styles.css';
    import ribbon from '../assets/ribbon-chart.svg'
</script>

<div class="graph-title">
    Toronto Land Cover Transitions
</div>

<div id="legend-ribbon">
    <svg width="360px" height="25px">
        <rect x="300" y="0" width="60" height="25" fill="#FFFFFF" stroke="#E1E1E1" stroke-width="1"/>
        <rect x="240" y="0" width="60" height="25" fill="#D0D1C9" stroke="#FFFFFF" stroke-width="1"/>
        <rect x="180" y="0" width="60" height="25" fill="#6FC7EA" stroke="#FFFFFF" stroke-width="1"/>
        <rect x="120" y="0" width="60" height="25" fill="#0D534D" stroke="#FFFFFF" stroke-width="1"/>
        <rect x="60" y="0" width="60" height="25" fill="#00A189" stroke="#FFFFFF" stroke-width="1"/>
        <rect x="0" y="0" width="60" height="25" fill="#f1c500" stroke="#FFFFFF" stroke-width="1"/>
        <text id="legend-label" x="30" y="17" text-anchor="middle">Built</text>
        <text id="legend-label-dark" x="90" y="17" text-anchor="middle">Grass</text>
        <text id="legend-label-dark"x="150" y="17" text-anchor="middle">Forest</text>
        <text id="legend-label" x="210" y="17" text-anchor="middle">Water</text>
        <text id="legend-label" x="270" y="17" text-anchor="middle">Bare</text>
        <text id="legend-label" x="330" y="17" text-anchor="middle">No Data</text>
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
    width: 600px;
    padding-left: 0px;
    padding-right: 0px;
    border-bottom: solid 1px var(--brandDarkBlue);
}
img {
    width: 100%
}

.graph-title {
    text-align: center;
    font-size: 30px;
    font-family: 'TradeGothicBold', sans-serif;
    font-weight: normal;
    height: 20px;
    font-size: 20px;
    color: var(--brandDarkBlue);
    background-color: var(--brandWhite);
    border-top: solid 1px var(--brandDarkBlue);
    padding-top: 10px;
    max-width: 600px;
    margin: 0 auto;
    padding-left: 0px;
    margin-bottom: 0px;
    position: relative;
}

#legend-ribbon {
        margin: 0 auto;
        margin-top: 10px;
        margin-bottom: 10px;
        width: 360px;
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