import streamlit as st
import pandas as pd
import plotly.express as px

# -----------------------
# Page Configuration
# -----------------------
st.set_page_config(
    page_title="Data Dashboard",
    page_icon="📊",
    layout="wide"
)

# -----------------------
# Load Data
# -----------------------
@st.cache_data
def load_data():
    df = pd.read_csv("data_core.csv")
    return df

df = load_data()

st.title("📊 Data Dashboard")
st.markdown("Interactive dashboard created using Streamlit")

# -----------------------
# Sidebar Filters
# -----------------------
st.sidebar.header("Filters")

# Select numeric columns for analysis
numeric_cols = df.select_dtypes(include=['int64', 'float64']).columns.tolist()
categorical_cols = df.select_dtypes(include=['object']).columns.tolist()

selected_numeric = st.sidebar.selectbox("Select Numeric Column", numeric_cols)

if categorical_cols:
    selected_category = st.sidebar.selectbox("Select Category Column", categorical_cols)
else:
    selected_category = None

# -----------------------
# KPI Section
# -----------------------
st.subheader("📌 Key Metrics")

col1, col2, col3 = st.columns(3)

col1.metric("Total Rows", len(df))
col2.metric("Average", round(df[selected_numeric].mean(), 2))
col3.metric("Maximum", df[selected_numeric].max())

# -----------------------
# Charts Section
# -----------------------

st.subheader("📈 Visualizations")

# Row 1
col1, col2 = st.columns(2)

# Line Chart
with col1:
    fig_line = px.line(df, y=selected_numeric, title="Line Chart")
    st.plotly_chart(fig_line, use_container_width=True)

# Bar Chart
with col2:
    if selected_category:
        fig_bar = px.bar(df, x=selected_category, y=selected_numeric, title="Bar Chart")
    else:
        fig_bar = px.bar(df, y=selected_numeric, title="Bar Chart")
    st.plotly_chart(fig_bar, use_container_width=True)

# Row 2
col3, col4 = st.columns(2)

# Histogram
with col3:
    fig_hist = px.histogram(df, x=selected_numeric, title="Histogram")
    st.plotly_chart(fig_hist, use_container_width=True)

# Scatter Plot
with col4:
    if len(numeric_cols) > 1:
        second_numeric = numeric_cols[1]
        fig_scatter = px.scatter(df, x=selected_numeric, y=second_numeric, title="Scatter Plot")
        st.plotly_chart(fig_scatter, use_container_width=True)

# -----------------------
# Pie Chart Section
# -----------------------
if selected_category:
    st.subheader("🥧 Pie Chart")

    pie_data = df[selected_category].value_counts().reset_index()
    pie_data.columns = [selected_category, "Count"]

    fig_pie = px.pie(
        pie_data,
        names=selected_category,
        values="Count",
        title="Category Distribution"
    )

    st.plotly_chart(fig_pie, use_container_width=True)

# -----------------------
# Data Preview
# -----------------------
st.subheader("📄 Data Preview")
st.dataframe(df)

# -----------------------
# Footer
# -----------------------
st.markdown("---")
st.markdown("Built with ❤️ using Streamlit")
