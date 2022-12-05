# ItemDecoration_RecyclerView


< - - - -  MyItemDecoration that implements ItemDecoration - - - - >  

package com.enact.mytrainer.TrainerUtills;

import android.graphics.Canvas;
import android.graphics.Rect;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.LinearLayout;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import com.enact.mytrainer.R;


public class RecyclerSectionItemDecoration extends RecyclerView.ItemDecoration {

    private final int headerOffset;
    private final boolean sticky;
    private final SectionCallback sectionCallback;

    private View headerView;
    private LinearLayout header;
    private TextView text;

    public RecyclerSectionItemDecoration(int headerHeight, boolean sticky, @NonNull SectionCallback sectionCallback) {
        headerOffset = headerHeight;
        this.sticky = sticky;
        this.sectionCallback = sectionCallback;
    }

    @Override
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
        super.getItemOffsets(outRect, view, parent, state);

        int pos = parent.getChildAdapterPosition(view);
        /*if (sectionCallback.isSection(pos)) {
            outRect.top = headerOffset;
        }*/
    }

    @Override
    public void onDrawOver(Canvas c, RecyclerView parent, RecyclerView.State state) {
        super.onDrawOver(c, parent, state);

        if (headerView == null) {
            headerView = inflateHeaderView(parent);
            header = (LinearLayout) headerView.findViewById(R.id.list_item_section_text);
            text = (TextView) headerView.findViewById(R.id.tv);
            fixLayoutSize(headerView, parent);
        }

        CharSequence previousHeader = "";
        for (int i = 0; i < parent.getChildCount(); i++) {
            View child = parent.getChildAt(i);
            final int position = parent.getChildAdapterPosition(child);

            CharSequence title = sectionCallback.getSectionHeader(position);
            text.setText(title);

            if (!previousHeader.equals(title) /*|| sectionCallback.isSection(position)*/)
            {
                drawHeader(c, child, headerView);
                previousHeader = title;
            }
        }
    }

    private void drawHeader(Canvas c, View child, View headerView) {
        c.save();
        if (sticky) {
            c.translate(0, Math.max(0, child.getTop() - headerView.getHeight()+13));
        } else {
            c.translate(0, child.getTop() - headerView.getHeight());
        }
        headerView.draw(c);
        c.restore();
    }

    private View inflateHeaderView(RecyclerView parent) {
        return LayoutInflater.from(parent.getContext()).inflate(R.layout.recycler_section_header, parent, false);
    }

    /**
     * Measures the header view to make sure its size is greater than 0 and will be drawn
     * https://yoda.entelect.co.za/view/9627/how-to-android-recyclerview-item-decorations
     */
    private void fixLayoutSize(View view, ViewGroup parent) {
        int widthSpec = View.MeasureSpec.makeMeasureSpec(parent.getWidth(), View.MeasureSpec.EXACTLY);
        int heightSpec = View.MeasureSpec.makeMeasureSpec(parent.getHeight(), View.MeasureSpec.UNSPECIFIED);

        int childWidth = ViewGroup.getChildMeasureSpec(widthSpec, 0, view.getLayoutParams().width);
        int childHeight = ViewGroup.getChildMeasureSpec(heightSpec, 0, view.getLayoutParams().height);

        view.measure(childWidth,
                childHeight);

        view.layout(0,
                0,
                view.getMeasuredWidth(),
                view.getMeasuredHeight());
    }

    public interface SectionCallback {
        boolean isSection(int position);
        CharSequence getSectionHeader(int position);

    }
}


< - - - -  MyItemDecoration that implements ItemDecoration finish here - - - - >  

< - - - - MainActivity that create object of MyItemDecoration with custom SectionCallback interface as an argument - - - - >


        val sectionItemDecoration = RecyclerSectionItemDecoration(resources.getDimensionPixelSize(com.google.firebase.firestore.R.dimen.notification_small_icon_size_as_large), true,  // true for sticky, false for not
                object : RecyclerSectionItemDecoration.SectionCallback {

                    override fun isSection(position: Int): Boolean {
                        return addAlphabets(chatModelList)
                    }

                    override fun getSectionHeader(position: Int): CharSequence? {
                      val time =   chatModelList.get(position).timestamp?.time?.let {
                          val timee = if (iSToday(it)) {
                                 "Today"
                            } else {
                              chatModelList.get(position).timestamp?.time?.let { time ->
                                  convertMilisToDate(time)
                              }

                            }
                          return@let timee
                        }

                        return time
                    }
                })
                
< - - - - sectionItemDecoration finishes here - - - - >
