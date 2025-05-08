import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import requests
import yfinance as yf
from datetime import datetime
 
class EconomicAnalyzer:
    """
    经济数据分析工具类
    功能包括：
    - 数据获取（API/本地文件）
    - 数据清洗和预处理
    - 统计分析
    - 数据可视化
    - 基本经济指标计算
    """
    
    def __init__(self):
        self.data = None
        self.processed_data = None
        self.visualization_style = 'seaborn'
        self._setup_visualization()
    
    def _setup_visualization(self):
        """设置可视化样式"""
        if self.visualization_style == 'seaborn':
            sns.set_style("whitegrid")
            plt.rcParams['figure.figsize'] = (12, 6)
        elif self.visualization_style == 'matplotlib':
            plt.style.use('ggplot')
    
    def load_data(self, source='api', file_path=None, ticker=None, start_date=None, end_date=None):
        """
        加载数据
        参数:
            source: 数据来源 ('api' 或 'file')
            file_path: 本地文件路径 (当source='file'时必需)
            ticker: 股票代码 (当从Yahoo Finance获取数据时)
            start_date: 开始日期 (YYYY-MM-DD)
            end_date: 结束日期 (YYYY-MM-DD)
        """
        if source == 'api':
            if ticker:
                return self._load_from_yfinance(ticker, start_date, end_date)
            else:
                raise ValueError("当source='api'时，必须提供ticker参数")
        elif source == 'file':
            if file_path:
                return self._load_from_file(file_path)
            else:
                raise ValueError("当source='file'时，必须提供file_path参数")
        else:
            raise ValueError("不支持的source参数，请使用'api'或'file'")
    
    def _load_from_file(self, file_path):
        """从文件加载数据"""
        try:
            if file_path.endswith('.csv'):
                self.data = pd.read_csv(file_path)
            elif file_path.endswith('.xlsx') or file_path.endswith('.xls'):
                self.data = pd.read_excel(file_path)
            else:
                raise ValueError("不支持的文件格式，请使用CSV或Excel文件")
            self.processed_data = self.data.copy()
            return True
        except Exception as e:
            print(f"加载文件时出错: {e}")
            return False
