<template>
  <div>
    <Bar id="my-chart-id" :options="chartOptions" :data="chartData" />
    <div id="chartjs-tooltip" style="opacity: 0; position: absolute"></div>
  </div>
</template>
<script>
import { Bar } from "vue-chartjs";
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from "chart.js";

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale);

export default {
  name: "BarChart",
  components: { Bar },
  data() {
    return {
      chartData: {
        labels: ["January", "February", "March"],
        datasets: [{ data: [40, 20, 12] }],
      },
      chartOptions: {
        responsive: true,
        plugins: {
          tooltip: {
            enabled: false,
            external: function (context) {
              var tooltipEl = document.getElementById("chartjs-tooltip");

              if (!tooltipEl) {
                tooltipEl = document.createElement("div");
                tooltipEl.id = "chartjs-tooltip";
                document.body.appendChild(tooltipEl);
              }

              if (context.tooltip.opacity === 0) {
                tooltipEl.style.opacity = 0;
                return;
              }

              // Set Text
              if (context.tooltip.body) {
                var titleLines = context.tooltip.title || [];
                var bodyLines = context.tooltip.body.map((b) => b.lines);

                var innerHtml = '<div class="title">' + titleLines.join("\n") + "</div>";
                bodyLines.forEach(function (body, i) {
                  var colors = context.tooltip.labelColors[i];
                  var style = "background:" + colors.backgroundColor;
                  style += "; border-color:" + colors.borderColor;
                  style += "; border-width: 2px";
                  var span = '<span class="chartjs-tooltip-key" style="' + style + '"></span>';
                  innerHtml += '<div class="body">' + span + body + "</div>";
                });
                // Add an icon to the tooltip
                innerHtml += '<i class="pi pi-check"></i>';
                tooltipEl.innerHTML = innerHtml;
              }

              var { offsetLeft: positionX, offsetTop: positionY } = context.chart.canvas;

              tooltipEl.style.opacity = 1;
              tooltipEl.style.position = "absolute";
              tooltipEl.style.left = positionX + context.tooltip.caretX + "px";
              tooltipEl.style.top = positionY + context.tooltip.caretY + "px";
              tooltipEl.style.fontFamily = context.tooltip.options.bodyFont.family;
              tooltipEl.style.fontSize = context.tooltip.options.bodyFont.size + "px";
              tooltipEl.style.fontStyle = context.tooltip.options.bodyFont.style;
              tooltipEl.style.padding =
                context.tooltip.options.padding + "px " + context.tooltip.options.padding + "px";
            },
          },
        },
      },
    };
  },
};
</script>

<style scoped>
#my-chart-id {
  width: 95vw;
  height: 95vh;
}
</style>
