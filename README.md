# 2024GP- 그래픽스 지형 만들기 및 작품
컴퓨터공학과 20210817 신현진 
-------------
                                let cols, rows;        
                                let scl = 20;        
                                let w = 1400;        
                                let h = 1400;        
                                let flying = 0;         
                                let terrain = [];        
                                        
                                let posX = 0;        
                                let posY = 0;        
                                let posZ = 0;        
                                let velX = 0;        
                                let velY = 0;        
                                let velZ = 0;        
                                let acc = 1; // 가속도        
                                let damping = 0.95; // 감속        
                                let moving = {        
                                  up: false,        
                                  down: false,        
                                  left: false,        
                                  right: false,        
                                  forward: false,        
                                  backward: false        
                                };        
                                        
                                function setup() {        
                                  createCanvas(1000, 1400, WEBGL);        
                                  cols = w / scl;        
                                  rows = h / scl;        
                                        
                                  for (let x = 0; x < cols; x++) {        
                                    terrain[x] = [];        
                                    for (let y = 0; y < rows; y++) {        
                                      terrain[x][y] = 0; //specify a default value for now        
                                    }        
                                  }        
                                  noStroke();        
                                }        
                                        
                                function draw() {        
                                  // 지형 생성        
                                  let angle = map(mouseX, 0, width, -PI / 6, PI / 6); // 마우스 X 위치에 따라 회전 각도 조정        
                                  angle = constrain(angle, -PI / 6, PI / 6); // 회전 각도를 일정 범위 내로 제한        
                                  rotateY(angle);        
                                        
                                  flying -= 0.1;        
                                  let yoff = flying;        
                                  for (let y = 0; y < rows; y++) {        
                                    var xoff = 0;        
                                    for (let x = 0; x < cols; x++) {        
                                      terrain[x][y] = map(noise(xoff, yoff), 0, 1, -100, 100);        
                                      xoff += 0.2;        
                                    }        
                                    yoff += 0.2;        
                                  }        
                                          
                                  ambientLight(60);        
                                                
                                  // add point light to showcase specular material        
                                  let locX = mouseX - width / 2;        
                                  let locY = mouseY - height / 2;        
                                  pointLight(255, 255, 255, locX, locY, 50);        
                                        
                                  specularMaterial(250);        
                                  shininess(50);        
                                          
                                  // 배경 및 지형 그리기        
                                  if (mouseY < height / 2) {        
                                    background(0, 128, 255); // 마우스 Y축이 낮을 때 회색 배경        
                                  } else {        
                                    background(128); // 마우스 Y축이 높을 때 하늘색 배경        
                                  }        
                                  rotateX(PI / 3);        
                                          
                                  fill(164, 164, 164, 196);        
                                  translate(-w / 2, -h / 2);        
                                  for (let y = 0; y < rows - 1; y++) {        
                                    beginShape(TRIANGLE_STRIP);        
                                    for (let x = 0; x < cols; x++) {        
                                      let v = terrain[x][y];        
                                      fill(v + 0, 80, 0, v + 200);      // 색상 및 투명도 색상은 초록색으로 만들고 투명도는 좀 진하게 나타내기위해 200으로 설정  
                                      vertex(x * scl, y * scl, terrain[x][y]);        
                                      vertex(x * scl, (y + 1) * scl, terrain[x][y + 1]);        
                                    }        
                                    endShape();        
                                  }        
                                
                                  
                                  // 비행기 이동        
                                  if (moving.forward) {        
                                    velZ -= acc;        
                                  }        
                                  if (moving.backward) {        
                                    velZ += acc;        
                                  }        
                                  if (moving.left) {        
                                    velX -= acc;        
                                  }        
                                  if (moving.right) {        
                                    velX += acc;        
                                  }        
                                  if (moving.up) {        
                                    velY -= acc;        
                                  }        
                                  if (moving.down) {        
                                    velY += acc;        
                                  }        
                                        
                                  posX += velX;        
                                  posY += velY;                
                                  posZ += velZ;                
                                        
                                  // 속도 감소        
                                  velX *= damping;        
                                  velY *= damping;        
                                  velZ *= damping;        
                                        
                                  push();        
                                  translate(posX, posY, posZ);        
                                        
                                  // 비행기 그리기        
                                    push();        
                                    translate(700,400, 150);        
                                    drawAirplane();        
                                    pop();        
                                  pop();        
                                }        
                                        
                                function drawAirplane() {        
                                  // 바디        
                                  push();        
                                  fill("#00BFFF");        
                                  ellipsoid(30, 80, 30);        
                                  pop();        
                                        
                                  // 뒷부분 꼬리날개                
                                  push();        
                                  fill("#0404B4");        
                                  translate(30, 50);                
                                  rotateZ(-PI / 2);        
                                  cone(30, 65, 10, 16);        
                                  pop();        
                                        
                                  // 뒷부분 꼬리날개        
                                  push();        
                                  fill("#0404B4");        
                                  translate(-30, 50);        
                                  rotateZ(PI / 2);        
                                  cone(30, 65, 10, 16);        
                                  pop();        
                                                
                                  // 뒷부분 꼬리날개 윗부분                
                                  push();        
                                  fill("#0404B4");        
                                  translate(0, 60, 40);        
                                  rotateX(PI / 2);        
                                  cone(10, 40, 14, 16);        
                                  pop();        
                                        
                                  // 왼쪽 엔진        
                                  push();        
                                  fill("#81DAF5");        
                                  translate(-70, -10);        
                                  rotateZ(PI / 2);        
                                  cone(30, 120, 10, 16);        
                                  pop();        
                                        
                                  // 오른쪽 엔진        
                                  push();        
                                  fill("#58D3F7");        
                                  translate(70, -10);        
                                  rotateZ(-PI / 2);        
                                  cone(30, 120, 10, 16);        
                                  pop();        
                                }        
                                        
                                function keyPressed() {        
                                  if (keyCode === 83) { // w 키        
                                    moving.forward = true;        
                                  } else if (keyCode === 87) { // s 키        
                                    moving.backward = true;        
                                  } else if (keyCode === 65) { // a 키        
                                    moving.left = true;        
                                  } else if (keyCode === 68) { // d 키        
                                    moving.right = true;        
                                  } else if (keyCode === 81) { // q 키        
                                    moving.up = true;        
                                  } else if (keyCode === 69) { // e 키        
                                    moving.down = true;        
                                  }        
                                }        
                                        
                                function keyReleased() {        
                                  if (keyCode === 83) { // w 키        
                                    moving.forward = false;        
                                  } else if (keyCode === 87) { // s 키        
                                    moving.backward = false;        
                                  } else if (keyCode === 65) { // a 키        
                                    moving.left = false;        
                                  } else if (keyCode === 68) { // d 키        
                                    moving.right = false;        
                                  } else if (keyCode === 81) { // q 키        
                                    moving.up = false;        
                                  } else if (keyCode === 69) { // e 키        
                                    moving.down = false;        
                                  }        
                                }        

-----------------
소감문        
안녕하십니까! 교수님 그래픽스 수업을 수강 중인 신현진입니다! 이번 그래픽스 과제를 하면서 느낀 점은 지형이라는 조금은 다소 복잡할 수 있는 기존에 배우던 것에 난이도가 조금 올라간 코드를 다루다 보니 오류도 많았고 저의 뜻대로 되지 않는 부분이 많았습니다. 또한 이번에 비행기를 만드는 과정에서 저번 과제는 2D였어서 translate를 사용하더라도 XY 축으로만 이동시키면 됐지만 지금은 3D라 Z축까지 신경을 써야 하니 특히 시간이 오래 걸렸던 거 같습니다. 하지만 난관에 부딪히고 계속해서 찾아보고 오류를 수정하면서 격파해 나갈 때마다 희열을 느낄 수 있었습니다. 이러한 경험, 시간이 걸리더라도 계속해서 도전하는 도전정신과 인내심, 다된 작품을 동영상 찍으며 테스트할 때 성취감은 어제의 저 보다 오늘의 자신이 더욱더 성장했다는 것을 체감하고 있습니다. 이러한 성장의 발판을 마련해 주신 교수님께 감사의 인사를 올립니다. 그리고 더욱더 교수님의 강의를 통해 처음에 과제에서 말씀드렸던 것처럼 언젠가는 훌륭한 작품을 하나 만드는 것이 저의 새로운 목표가 되었습니다. 감사합니다!
---------------
자가 채점 
1. 제출 2점 (완료했습니다.)
2. 색상 및 투명도 +1 (지형의 색상을 초록색으로 변경하고 투명도가 원래 v+100 이지만 너무 연하고 밖의 색상이 비쳐 보여서 v+200으로 변경하였습니다.)
3. 재질 +1 (재질은 specularMaterial()을 사용하여 비행기의 재질을 변경하였습니다.)
4. 조명 +1 (조명은 ambientLight(60); 를 사용하여 Y축 기준 마우스 포인트를 위로 올렸을때 해가 떠 있는거 처럼 표현하고 Y축기준 아래로 내리면 해가 지는것을 처럼 검은색 이 되게 표현하였습니다.)
5. 기타 : 조명은 Y 축에따라 꺼졌다 켜지지만 배경은 그대로 남으니 Y축 위로는 하늘색 Y축 아래로는 회색으로 표현하였습니다.
------------------
동영상은 p5.js 지형 만들기.mp4로 첨부했습니다.        
동영상 크기가 6mb라 미리보기로 보는것이 불가능합니다.        
p5.js 지형 만들기.mp4를 클릭하셔서        
View raw 를 클릭하시면 파일을 다운하셔서 동영상을 보실수 있습니다.
추가로 Add files via upload        
로 들어가시면 설명을 보실수있습니다. 

