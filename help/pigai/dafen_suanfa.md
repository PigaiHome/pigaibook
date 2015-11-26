# 打分公式算法

##1.打分

分数=原始分+惩罚函数分-

####1.1 单个子维度子维度打分
```php
#单维度打分（百分制）， 2011.3.24 zhangyue
function score_by_single_dim($valu, &$cfg){
	if($valu <= $cfg['min']) return 0;
	if($valu >= $cfg[max]) return 100;
	if( $valu < $cfg[mean_min])
	    return $this->line_score($cfg[min], $cfg[mean_min], $valu) * $cfg[mean_min_score] ; # 低于60分档
	if( $valu >= $cfg[mean_min] && $valu < $cfg[mean])
	    return $this->line_score($cfg[mean_min], $cfg[mean], $valu) * ($cfg[mean_score] - $cfg[mean_min_score]) + $cfg[mean_min_score];
	if( $valu >= $cfg[mean] && $valu <= $cfg[mean_max])
	    return $this->line_score($cfg[mean], $cfg[mean_max], $valu) * ($cfg[mean_max_score] - $cfg[mean_score]) + $cfg[mean_score];
	if( $valu > $cfg[mean_max])
	    return $this->line_score($cfg[mean_max], $cfg[max], $valu) * (100 - $cfg[mean_max_score]) + $cfg[mean_max_score] ; #90
	return 0; //// un-reachable path assumed
}
# 返回值必须在[0..1]之间 zhangyue
function line_score ( $min, $max, $valu){
	$fen= ($max - $min) ;
	if($fen<=0) return 0;
	return min(1, (max($valu, $min) - $min) / $fen );
}
function log_score ( $min, $max, $valu) {
	$fen= log($max) - log($min) ;
	if($fen<=0) return 0;
	return min(1, (log(max($valu, $min)) - log($min)) /$fen );
}
```
