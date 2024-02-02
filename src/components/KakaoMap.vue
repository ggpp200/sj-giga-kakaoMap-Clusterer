<template>
  <div id="map" style="width:100%;height:720px; margin:0px;"></div>
	
</template>

<script>
import { onMounted } from 'vue'
import { useKakaoStore } from '@/stores/kakao.js'
import markImage from '@/assets/marker.png'
import $ from 'jquery'
export default {
	setup() {
		const store = useKakaoStore()
		/* global kakao */
		onMounted(() => {
			
			if (window.kakao && window.kakao.maps) {
				initMap();
			} else {
				const script = document.createElement("script")
				script.onload = () => kakao.maps.load(initMap)
				script.src ="key를 넣어주세요";
				script.type = "text/javascript"
				document.head.appendChild(script)
			}
		})
		const initMap = () => {
			
			const container = document.getElementById("map")
      
			const options = {
				center: new kakao.maps.LatLng(36.36880618678187,127.37618869404398),
				level: 5,
			} 
			initMap.map = new kakao.maps.Map(container, options)
			var clusterer = new kakao.maps.MarkerClusterer({
				map: initMap.map, // 마커들을 클러스터로 관리하고 표시할 지도 객체
				averageCenter: true, // 클러스터에 포함된 마커들의 평균 위치를 클러스터 마커 위치로 설정
				minLevel: 2,// 클러스터 할 최소 지도 레벨
				disableClickZoom: true // 클러스터 마커를 클릭했을 때 지도가 확대되지 않도록 설정한다
				
			})



			// 저장해둔 마커 위치 불러와서 마커 붙이기
			var markers = $(store.s_markers_info).map(function(i,item) {
						var imageSrc = markImage, // 마커이미지의 주소입니다    
								imageSize = new kakao.maps.Size(30, 39), // 마커이미지의 크기입니다
								imageOption = {offset: new kakao.maps.Point(27, 69)}; // 마커이미지의 옵션입니다. 마커의 좌표와 일치시킬 이미지 안에서의 좌표를 설정합니다.
            return new kakao.maps.Marker({
                position : new kakao.maps.LatLng(item[1], item[2]),
								image : new kakao.maps.MarkerImage(imageSrc,imageSize,imageOption)
            });
        });
			clusterer.addMarkers(markers);
			
			

			// 마커 클러스터러에 클릭이벤트를 등록합니다
			// 마커 클러스터러를 생성할 때 disableClickZoom을 true로 설정하지 않은 경우
			// 이벤트 헨들러로 cluster 객체가 넘어오지 않을 수도 있습니다
			kakao.maps.event.addListener(clusterer, 'clusterclick', function(cluster) {
				console.log( cluster.getMarkers());
					// 현재 지도 레벨에서 1레벨 확대한 레벨
					var level = initMap.map.getLevel()-1;

					// 지도를 클릭된 클러스터의 마커의 위치를 기준으로 확대합니다
					initMap.map.setLevel(level, {anchor: cluster.getCenter()});

			});
		}
		
		return {
		}
	}
}
</script>

<style>

</style>